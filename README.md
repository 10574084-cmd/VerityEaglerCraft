# VerityEaglerCraft

Port Of The Mod Of Verity In EaglerCraft 

#Setup
Install the file and make a key in groq and fish audio Sign up first and in https://fish.audio/es/app/discovery/ search for the model of the
verity voice copy the verity voice
https://console.groq.com/keys
https://fish.audio/es/app/api-keys/
https://fish.audio/es/app/discovery/
copy the key And DONT LOSE IT save it in a txt file

then go to the eagler and then put the api keys in the config of the apis and the model
then the groq will function but voice not you need a proxy
go to cloudflare sign up and make a worker click start with hello world an then deploy
then go to the hello world proxy an copy

// Proxy gratuito para Verity (Cloudflare Workers) - v3.
// Hace la llamada a Fish Audio por ti y agrega los permisos CORS que Fish no da.
// Solo reenvia a https://api.fish.audio/v1/tts (hablar) y /model (buscar voces por nombre). No guarda nada: tu clave solo pasa de largo.
export default {
  async fetch(request) {
    const cors = {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Methods': 'GET, POST, OPTIONS',
      'Access-Control-Allow-Headers': 'authorization, content-type, model',
      'Access-Control-Max-Age': '86400'
    };
    if (request.method === 'OPTIONS') return new Response(null, { status: 204, headers: cors });
    const auth = request.headers.get('authorization');

    if (request.method === 'GET') {
      const sp = new URL(request.url).searchParams, q = sp.get('verity_search'), lang = sp.get('verity_lang');
      if (q === null) return new Response('Proxy de Verity para Fish Audio v3: listo (con busqueda de voces).', { status: 200, headers: cors });
      if (!auth) return new Response('Falta la clave', { status: 401, headers: cors });
      let r;
      try { r = await fetch('https://api.fish.audio/model?page_size=20&sort_by=score&title=' + encodeURIComponent(q.slice(0, 80)) + (/^[a-z]{2}$/.test(lang || '') ? '&language=' + lang : ''), { headers: { Authorization: auth } }); }
      catch (e) { return new Response('No se pudo conectar con Fish Audio', { status: 502, headers: cors }); }
      const o = new Headers(cors); o.set('Content-Type', 'application/json');
      return new Response(r.body, { status: r.status, headers: o });
    }
    if (request.method !== 'POST') return new Response('Metodo no permitido', { status: 405, headers: cors });

    const body = await request.text();
    if (body.length > 20000) return new Response('Peticion demasiado grande', { status: 413, headers: cors });

    const h = new Headers({ 'Content-Type': 'application/json' });
    const model = request.headers.get('model');
    if (auth) h.set('Authorization', auth);
    if (model) h.set('model', model);

    let up;
    try { up = await fetch('https://api.fish.audio/v1/tts', { method: 'POST', headers: h, body }); }
    catch (e) { return new Response('No se pudo conectar con Fish Audio', { status: 502, headers: cors }); }

    const out = new Headers(cors);
    out.set('Content-Type', up.headers.get('content-type') || 'application/octet-stream');
    return new Response(up.body, { status: up.status, headers: out });
  }
};

put it in the proxy 
and then put the proxy iin eagler and the voice can function normaly

enjoy

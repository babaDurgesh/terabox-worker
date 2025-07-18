export default {
  async fetch(request, env, ctx) {
    if (request.method !== "POST") {
      return new Response(JSON.stringify({ error: "Only POST allowed" }), {
        status: 405,
        headers: { "Access-Control-Allow-Origin": "*", "Content-Type": "application/json" }
      });
    }

    try {
      const incoming = await request.json();

      const forward = await fetch("https://hex.teraboxfast2.workers.dev/", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(incoming)
      });

      const result = await forward.text();
      return new Response(result, {
        headers: {
          "Access-Control-Allow-Origin": "*",
          "Content-Type": "application/json"
        }
      });

    } catch (err) {
      return new Response(JSON.stringify({ error: err.message || "Internal error" }), {
        status: 500,
        headers: {
          "Access-Control-Allow-Origin": "*",
          "Content-Type": "application/json"
        }
      });
    }
  }
}

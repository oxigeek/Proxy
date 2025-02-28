const TOKEN = `Bearer ${Deno.env.get("token")}`;

Deno.serve(async (req: Request) => {
  const targetUrl = req.headers.get("x-target-url");
  const token = req.headers.get("Authorization");
  const contentType = req.headers.get("Content-Type")

  if (contentType !== "application/json") return new Response("PROXY contentType Not Allowed", { status: 404 });

  if (req.method !== "POST") return new Response("PROXY Method Not Allowed", { status: 405 });

  if (!targetUrl || !token) return new Response("PROXY Bad Request", { status: 400 });

  if (token !== TOKEN) return new Response("PROXY Unauthorized", { status: 401 });

  try {
    const { body, headers } = await req.json();

    const proxyResponse = await fetch(targetUrl, {
      method: req.method,
      headers: new Headers(headers || {}), 
      body: body ? JSON.stringify(body) : null, 
    });

    const responseBody = proxyResponse.status === 204 || proxyResponse.status === 304
    ? null 
    : await proxyResponse.text();

    return new Response(responseBody, {
      status: proxyResponse.status,
      headers: proxyResponse.headers,
    });
  } catch (error) {
    console.error("Erreur de proxy :", error);
    return new Response("Internal Server Error", { status: 500 });
  }
});

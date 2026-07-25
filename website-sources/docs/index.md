---
template: main.html
---
<script>
  (function() {
    // Récupère la langue du navigateur (ex: "fr", "fr-FR", "en-US")
    var userLang = navigator.language || navigator.userLanguage || "en";
    
    // Si la langue commence par "fr", on redirige vers /fr/
    if (userLang.toLowerCase().startsWith("fr")) {
      window.location.href = "/fr/";
    } else {
      // Pour toutes les autres langues, on redirige par défaut vers /en/
      window.location.href = "/en/";
    }
  })();
</script>

<noscript>
  <!-- Fallback si le JS est désactivé sur le navigateur client -->
  <meta http-equiv="refresh" content="0; url=/en/" />
  <p>Redirecting to <a href="/en/">English version</a> / Redirection vers la <a href="/fr/">version française</a>.</p>
</noscript>
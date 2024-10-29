---
title: Automatisert testing av API'er beskyttet med ID-porten-tokens
description: Automatiserte tester
summary: "Automatiserte tester"

sidebar: oidc
product: ID-porten
redirect_from: /idporten_headless_login
---

## Automatisert testing av API'er beskyttet med ID-porten tokens

ID-porten i testmiljøer tilbyr "headless login" der tokens kan utstedes uten brukerinteraksjon.  Denne funksjonalitetem skal kun brukes til å forenkle testing av API'er beskyttet av ID-porten tokens.

I denne forenklede flyten kan en syntetisk testbruker logges inn automatisk med en syntetisk testbruker ved å sende inn et `login_hint` og liste over `scope` samt øvrige vanlige parametre i en [authorization request]({{site.baseurl}}/docs/idporten/oidc/oidc_protocol_authorize) til ID-porten.

 Parameter | Beskrivelse | Eksempel
 -|-|-|
 `login_hint` | Angir hvilken syntetisk personidentifikator som skal brukes samt sikkerhetsnivå som ønskes | `login_hint=testid:12345678901_idporten-loa-high`
 `scope`      | Angir hvilke scope som skal implisitt samtykkes til | `scope=openid profile mitt:api_scope`

Klienten leser authorization response fra location header og plukker ut code (og verifiserer state).  Klienten kaller deretter [token-endepunktet]({{site.baseurl}}/docs/idporten/oidc/oidc_protocol_token) på vanlig måte.

 <div class="mermaid">
sequenceDiagram
  Klient ->> OpenID Provider: HTTP GET Redirect autentiseringsforespørsel med login_hint
  note over OpenID Provider: Automatisk autentisering basert på login_hint og implisitt samtykker til førespurte scopes
  OpenID Provider ->> Klient: HTTP response med location header med autorisasjonscode
  Klient ->> OpenID Provider: forespørre token (/token)
  OpenID Provider ->> Klient: id_token + access_token (evt. refresh_token)
  Klient ->> API: bruke API med access_token
  API ->> OpenID Provider: validere token
  OpenID Provider ->> API: token informasjon
  API->>Klient: Resultat av API-operasjon
 </div>


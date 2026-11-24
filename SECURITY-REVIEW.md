# Security review

Reviewed: 2026-11-24

## Scope and summary

Read-only review of `index.html` and the connected Supabase project `csw-sports` (`abzaisrkoqexyxupxbzm`). The review identified two high-severity and three medium-severity findings. This report does not include credentials or secret values.

## Findings

| # | Severity | Location | Vulnerability | Confidence |
|---|----------|----------|---------------|------------|
| 1 | HIGH | `index.html:4851`; `public.csw_chat_reactions` | Untrusted reaction text is interpolated into HTML and an inline event handler. A chat participant may be able to inject markup or script that executes for other chat members. | 9/10 |
| 2 | HIGH | `index.html:4907–4912`; `team-photos` bucket | Chat attachments use a public bucket and public URLs. This permits access to attachments without the team-membership checks protecting chat messages. | 9/10 |
| 3 | MEDIUM | Supabase `storage.objects` policies | Broad authenticated upload and delete policies check only the bucket ID. Since permissive policies combine with OR, they may allow any authenticated user to upload or delete team media, bypassing a separate staff-only policy. | 9/10 |
| 4 | MEDIUM | `index.html:2328`; `public.csw_forces_state` | The shared payload includes student excusal details (names, dismissal times, reasons, and staff). An anonymous read policy exposes the `primary` row. No current excusal records were confirmed exposed. | 8/10 |
| 5 | MEDIUM | Supabase Auth configuration | Leaked-password protection is disabled. This is a configuration weakness; no direct compromise was verified. | — |

## Recommended remediation

1. Validate reaction values against a server-side allowlist. Render reaction content with safe DOM/text APIs and remove inline event handlers.
2. Move chat attachments to a private bucket. Enforce membership-based storage access and serve files through authenticated access or short-lived signed URLs.
3. Remove broad authenticated upload/delete policies. Enforce staff and team authorization in storage policies.
4. Move student excusal details to a separate table protected by staff-scoped row-level security, or exclude them from the anonymously readable state payload.
5. Enable leaked-password protection in Supabase Auth.

## Verification limits

This was a read-only review. Findings 1–4 are based on the reported application and live database policy evidence. Finding 5 is based on the Supabase Security Advisor configuration notice. No exploitation was performed, and no data was changed.

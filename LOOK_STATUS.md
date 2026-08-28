# LOOK STATUS — YSP Forgot password on Sign in

**LOOK ONLY.** Nothing merged, no branch pushed, no app PR, no migration, no navy deploy.
`resetPasswordForEmail` was never called and no email was sent to anyone. The working tree is
clean — the After screens were captured from the real app and then reverted.

## Three-way delivery
1. **Sheet + enlargeable PNGs** — `/workspace/ysp-forgot-password-look/sheet.html` (+ `shots/`)
2. **Zip** — `/workspace/YSP-forgot-password-LOOK.zip`
3. **Hosted** — see *Hosting* below.

## Delta

A parent who forgets their password has no way back in today: there is no control on the sign-in
form and no page behind one. The proposal adds both, inside the auth chrome that already exists.
**On Sign in**, a *Forgot password?* link goes on the password label row, right-aligned — the
Squarespace / Dropbox / GitHub pattern, using the same brand link treatment as the existing *Sign
up* link. It costs nothing: measured at 1440px the card is **416 × 282px before and after**, the
Sign in button stays at y=406, and the label and link share a baseline exactly. **The reset page**
reuses the `Welcome back` chrome — heading, one line of help, the same Card, the same input and
full-width brand button, one way back. After submit the card shows a single neutral line, *"If that
email is on file, we've sent a link to reset your password"*, which never reveals whether an
address has an account, plus one action. It reuses the notice styling `AuthForm` already renders
for `state.notice`, so no new component is introduced. Sign up is untouched and the light theme
holds with no extra work.

Verified in code rather than assumed: `AuthForm.tsx` has no such control, `src/app/auth/` has no
`forgot-password` route, and `resetPasswordForEmail` has **zero call sites**. But the back half of
the flow is already built and tested — `callback.ts:62` keys off `type=recovery`, `callback.test.ts`
asserts recovery verifies straight to `/auth/set-password`, and `siteUrl.ts:11` already exports
`authCallbackUrl("recovery")`. The build is one route, one link, and one `resetPasswordForEmail`
call pointed at that helper.

## Question for Paul before any build
The send would be Supabase Auth's own recovery mail, not the app's email module. Worth confirming
the project's Auth SMTP is pointed at a real sender first — Supabase's built-in one is rate-limited
and meant for development, and because the success message is deliberately neutral a parent would
never learn their reset had not arrived. **A question to answer, not something observed broken.**

## Hosting
**Repo created and pushed. Pages not yet switched on — that one API call is blocked.**

- Repo: https://github.com/covertggtv-a11y/ysp-forgot-password-look (public, `main`, content pushed)
- Intended URL: **https://covertggtv-a11y.github.io/ysp-forgot-password-look/** — currently 404,
  because GitHub Pages has never been enabled on the repo (`GET .../pages` returns 404).

`gh repo create` went through this time; `gh api -X POST .../pages` is refused by the harness
permission classifier. One command from an approved shell finishes it:

    gh api -X POST repos/covertggtv-a11y/ysp-forgot-password-look/pages \
      -f 'source[branch]=main' -f 'source[path]=/'

Or in the browser: repo → Settings → Pages → Source `main` / `/ (root)` → Save. Either way the URL
above goes live within a minute or two; nothing else needs changing.

✅ Separately confirmed this session: the earlier **`ysp-home-symmetry-look`** repo now exists, its
Pages site is built, and https://covertggtv-a11y.github.io/ysp-home-symmetry-look/ returns 200 — so
the link cited in PR #12 does resolve. That flag is cleared.

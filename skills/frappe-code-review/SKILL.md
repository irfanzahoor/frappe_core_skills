---
name: frappe-code-review
description: Review Frappe/ERPNext code against Frappe Sensei's 79 deterministic checks (FR-001–FR-082) — placement, APIs, security, performance, server vs client. Use when reviewing code before merge or ship, or when asked to check code for bad patterns.
---

# Frappe Code Review

Review code a senior Frappe developer would accept: in the right file, using
the framework's own APIs, with permissions checked and no silent failures.
This skill is the agent-usable checklist for
[Frappe Sensei](https://github.com/irfanzahoor/frappe-sensei) — 79
deterministic checks, each with a bad example and a good example in the
canonical rule file. The table below is the offline checklist; the full
descriptions and examples live in the canonical source.

## Canonical source (single source of truth)

- Full rules (descriptions, bad/good examples, fix, source):
  https://github.com/irfanzahoor/frappe-sensei/blob/main/rules/rules.json
- Live tool (paste code, upload files, or give a public GitHub link):
  https://irfanzahoor.github.io/frappe-sensei/
- Rule documentation: https://github.com/irfanzahoor/frappe-sensei/blob/main/docs/RULES.md

Do not invent a rule ID. If a finding is not in the table below, it is not a
Sensei rule — say so, or mark it "verify" instead of stating it as fact.
Rule IDs FR-068, FR-073 and FR-077 do not exist (candidates that were
rejected for false positives); never use them.

## The 79-rule checklist

Severity: **blocker** = must fix before merge; **major** = fix in this
change; **minor** = fix when touching the file. Counts: 18 blocker,
49 major, 12 minor. Categories: placement 9, apis 27, security 27,
performance 11, server-client 5.

| ID | Severity | Category | Rule | Fix |
|---|---|---|---|---|
| FR-001 | blocker | placement | Own DocType logic in hooks.py or public/js | Move the logic into the DocType's own controller <name>.py / form script <name>.js. |
| FR-002 | blocker | placement | Another app's files edited directly | Never edit another app's files; extend via doc_events in hooks.py plus an overrides module and fixtures. |
| FR-003 | blocker | placement | Other app's DocType logic placed in hooks.py directly | Put the logic in <app>/<module>/overrides/<name>.py and reference it from doc_events in hooks.py. |
| FR-004 | blocker | placement | set_query outside setup/onload | Move frm.set_query into the setup (or onload) handler of the correct form script. |
| FR-005 | major | placement | Duplicate frappe.ui.form.on blocks | Merge the handlers into a single frappe.ui.form.on block per DocType. |
| FR-006 | major | placement | Duplicate controller methods | Merge the duplicate methods into one and extend the existing handler. |
| FR-007 | minor | placement | Custom Field for another app not shipped as a fixture | Ship the Custom Field / Property Setter as a fixture filtered by module. |
| FR-008 | major | apis | raise Exception instead of frappe.throw | Replace raise Exception with frappe.throw(_('...')). |
| FR-009 | major | apis | Untranslated user-facing string | Wrap every user-facing string in _() in Python or __() in JS. |
| FR-010 | major | apis | f-string inside _() | Use _() with {0} placeholders and .format() instead of an f-string. |
| FR-011 | minor | apis | print() left in Python code | Remove print() and use frappe.log_error for jobs and integrations. |
| FR-012 | minor | apis | console.log or alert() left in JS | Remove console.log/alert() and use frappe.show_alert for user feedback. |
| FR-013 | major | apis | get_doc used for a single field | Use frappe.db.get_value or frappe.get_cached_value for single fields. |
| FR-014 | major | apis | fields=["*"] in get_all / get_list | List the fields you need explicitly in fields. |
| FR-015 | major | apis | import statement in a Server Script | Remove the import and use the pre-loaded names (frappe.parse_json, frappe.utils.today, frappe.utils.flt). |
| FR-016 | major | apis | Bare except clause | Catch a named exception and handle or re-raise it with frappe.throw. |
| FR-017 | minor | apis | Doc values used without flt / cint / getdate | Wrap doc values with flt(), cint() or getdate() as appropriate. |
| FR-018 | blocker | server-client | Validation or business rule only in JS | Enforce the rule on the server in validate / before_submit; keep JS for UX only. |
| FR-019 | major | server-client | Form calls a method that is not whitelisted | Add @frappe.whitelist() to the controller method and check permissions before returning data. |
| FR-020 | minor | server-client | Field behaviour set in JS instead of the DocType | Set fetch_from, depends_on and mandatory in the DocType field settings, not in JS. |
| FR-021 | minor | server-client | frappe.call in refresh for rarely-changing data | Load rarely-changing data once in onload or frm.doc.__onload instead of calling on every refresh. |
| FR-022 | blocker | security | User input formatted into SQL (SQL injection) | Use %s placeholders with values passed separately, or frappe.qb. |
| FR-023 | blocker | security | Whitelisted method without a permission check | Check frappe.has_permission before returning or writing data in every whitelisted method. |
| FR-024 | blocker | security | allow_guest on a method that writes or returns private data | Remove allow_guest from any method that writes or returns private data; keep it only for truly public read-only endpoints. |
| FR-025 | major | security | ignore_permissions without an explanatory comment | Add a comment explaining why ignore_permissions is safe here, or remove it. |
| FR-026 | major | security | Hardcoded secret in code, fixture or JS | Move secrets to site_config.json with bench set-config and read them with frappe.conf. |
| FR-027 | major | security | User content in Jinja without escaping, or \|safe on user content | Escape user content with \|e and use \|safe only on HTML your own code generated. |
| FR-028 | major | security | User input inserted as HTML in JS | Build DOM with .text() or frappe.utils.escape_html() instead of .html(user_input). |
| FR-029 | blocker | performance | frappe.db.commit() in a controller, hook or request | Remove frappe.db.commit() from controllers, hooks and requests; it is allowed only in long background jobs and patches. |
| FR-030 | major | performance | Database call inside a loop (N+1) | Fetch in bulk before the loop, build a dict, then loop over the dict. |
| FR-031 | major | performance | Bulk update done doc-by-doc with save() | Use frappe.db.set_value with filters or a QB update for bulk changes, only when skipping controller validation is acceptable. |
| FR-032 | major | performance | Aggregation done in Python instead of SQL | Aggregate in SQL/QB with Sum or Count instead of loading rows into Python. |
| FR-033 | minor | performance | Slow work in a request without frappe.enqueue | Move slow work to frappe.enqueue with a queue and timeout. |
| FR-034 | minor | performance | Master data read without caching | Use frappe.get_cached_value or get_value with cache=True for rarely-changing masters. |
| FR-035 | minor | performance | Name-only list fetched without pluck | Use pluck='name' for simple lists of names. |
| FR-036 | blocker | apis | self.save() inside validate / before_save / on_update | Never call .save() from validate, before_save or on_update; use self.db_set() for a single field, or move the work to after_save or a background job. |
| FR-037 | major | apis | Field assigned in on_update is never saved | Set the field in validate or before_save, or use self.db_set() in on_update. |
| FR-038 | major | apis | Overridden validate() without super().validate() | Call super().validate() first in every overridden validate(). |
| FR-039 | blocker | security | frappe.throw() or raise inside a permission hook | Never throw or raise in a permission hook; return False to deny and True for users the hook does not restrict. |
| FR-040 | major | security | has_permission hook that can fall through to None | End every has_permission hook with an explicit return True for users the hook does not restrict; return False only to deny. |
| FR-041 | minor | apis | cur_frm used in client script | Use the frm parameter passed to every form event handler instead of cur_frm. |
| FR-042 | major | security | eval() or exec() in Python code | Parse data with frappe.parse_json or json.loads instead of eval/exec; legitimate uses in Frappe apps are near-zero. |
| FR-043 | minor | performance | frappe.enqueue() without queue= | Always pass queue= explicitly — 'short', 'default' or 'long' — to frappe.enqueue(). |
| FR-044 | blocker | security | Shell command built from app code (os.system / subprocess shell=True) | Use subprocess.run with a list of arguments and shell=False, or a framework API; never build a shell string from user or document data. |
| FR-045 | major | security | Unsafe deserialization (pickle / yaml.load / marshal / dill / shelve) | Use json.loads / frappe.parse_json for data, or yaml.load with Loader=yaml.SafeLoader. Never unpickle data you did not create yourself. |
| FR-046 | blocker | security | Server-side template injection (render_template with a non-literal template) | Pass a literal template path or string to render_template; render user-controlled content as data with \|e, never as the template itself. |
| FR-047 | blocker | apis | Delete or truncate with no filter (whole table cleared) | Always pass a concrete filter to frappe.db.delete, and a WHERE clause to raw DELETE; use truncate only as a deliberate, commented full-table clear. |
| FR-048 | major | apis | DDL in app code (sql_ddl / ALTER / CREATE / DROP / add_index / truncate) | Move schema changes into a patch (patches.txt + an execute() function) or into on_doctype_update; never run DDL from a whitelisted method or a lifecycle hook. |
| FR-049 | blocker | apis | Direct write to a ledger doctype (GL Entry / Stock Ledger / Bin / ...) | Never write ledger rows directly. Post, cancel or amend through the source document (Sales Invoice, Stock Entry, Payment Entry) and let the framework write the ledger. |
| FR-050 | blocker | apis | Monkey-patching frappe / erpnext / hrms at import time | Do not assign attributes on frappe, erpnext or hrms. Use a hook, an override module, or a wrapper function in your own app instead. |
| FR-051 | major | apis | frappe.enqueue() in a lifecycle or whitelisted method without enqueue_after_commit=True | Pass enqueue_after_commit=True to every frappe.enqueue() / enqueue_doc() called from a lifecycle method or a whitelisted method. |
| FR-052 | major | security | frappe.qb.get_query() without ignore_permissions=False | Pass ignore_permissions=False to frappe.qb.get_query in user-facing code, or use frappe.get_list, which applies permissions. |
| FR-053 | major | apis | Misspelled lifecycle method name (after_save / before_delete / on_saved ...) | Rename the method to a lifecycle name the framework calls (on_update, on_submit, before_insert, after_insert, on_trash, after_delete, on_cancel, on_change, autoname, onload, ...), or rename it to a plain helper name and call it from a lifecycle method. The same applies to doc_events keys in hooks.py. |
| FR-054 | major | apis | Request state read inside a lifecycle method (frappe.local.request / form_dict) | Read request data only in whitelisted methods and pass it in as an argument; lifecycle methods must work from the document alone. |
| FR-055 | major | apis | return with a value inside before_install | Never return a value from before_install; raise with frappe.throw if the install must stop, or return nothing at all. |
| FR-056 | major | placement | Dotted path in hooks.py / patches.txt does not resolve | Fix the dotted path to a module and function that exist in the app, or submit the whole app so the checker can resolve it. |
| FR-057 | major | placement | Database or site state read at module level in hooks.py | Keep hooks.py to static data only. Move per-site conditions into the handler body, or into a before_enable hook. |
| FR-058 | major | performance | Raw-SQL write without clearing the document cache | Call frappe.clear_document_cache(doctype, name) — or frappe.clear_cache(doctype=...) — in the same function after a raw SQL write. |
| FR-059 | major | security | frappe.set_user() without restoration, or frappe.session.user assignment | Restore the original user in a finally block after every set_user, or use frappe.only_for / a permission check instead of switching users. |
| FR-060 | blocker | security | Untyped or container-typed whitelisted parameter passed to an ORM query | Type every whitelisted parameter (str, int, list[str]) and validate it before the query; accept only scalar values for filter keys, and build filters from a fixed allowlist of fields. |
| FR-061 | major | security | Whitelisted write method declared as GET | Declare write methods with methods=["POST"] (or no GET restriction); never put a write behind a GET endpoint. |
| FR-062 | blocker | security | Whitelisted parameter named ignore_* reaches the ORM, or **kwargs forwarded to the ORM | Never accept ignore_* as a request parameter; decide server-side, from the user's roles, whether a permission check is skipped, and pass a literal False otherwise. |
| FR-063 | major | apis | Check-then-insert race: exists() followed by an insert of the same doctype | Insert first and catch frappe.UniqueValidationError, or put a unique index on the field and let the database enforce it; never trust a separate exists() check. |
| FR-064 | major | performance | db_set(..., commit=True) or frappe.db.auto_commit_on_many_writes | Drop commit=True and let the request commit once at the end; if a value must be visible to another request immediately, do the work in a background job instead. |
| FR-065 | major | security | TLS verification disabled (verify=False / CERT_NONE / unverified context) | Leave TLS verification on; if a local or self-signed certificate is needed, pass its CA bundle to verify= instead of turning verification off. |
| FR-066 | major | security | Weak hash (md5 / sha1) used for a password, token or secret, or compared with == | Hash passwords with frappe.utils.password or passlib, sign tokens with hmac.new(..., hashlib.sha256), and compare secrets only with hmac.compare_digest. Keep md5/sha1 only for cache keys and checksums. |
| FR-067 | major | security | Token or secret generated with random.* / uuid.uuid1 / time.time() | Generate tokens with secrets.token_hex / secrets.token_urlsafe, or frappe.generate_hash(); use uuid.uuid4 for random ids. |
| FR-069 | major | apis | Self-referential assignment in validate / before_save (value compounds on every save) | Compute the field from its inputs (row.base_qty, the rate, the document) instead of from its own current value; if a running total is needed, reset it to 0 first and sum the rows. |
| FR-070 | major | apis | except block that only passes, continues or logs around a write | Re-raise after logging, or roll back and return an explicit error to the caller; never swallow a failed write. |
| FR-071 | major | security | permission_query_conditions hook that can return "" / None (no restriction) | Return a real condition for every user, including the administrator ("1=1" when unrestricted); never return an empty string or fall off the end of the hook. |
| FR-072 | major | security | Direct write to docstatus or status with set_value / db_set | Change status through the document's own method (doc.submit(), doc.cancel(), or the DocType's status method) so validate and the workflow run; use set_value only for plain data fields. |
| FR-074 | major | security | Mass assignment: doc.update() with request data, or get_doc(json.loads(...)) | Copy only the fields the endpoint allows into a new dict (an explicit allowlist), validate each one, then update the document with that dict. |
| FR-075 | major | security | Open redirect: redirect_to / flags.redirect with a request value and no validation | Allow only relative paths or URLs on a fixed allowlist of hosts; validate with urlparse and reject anything that does not start with / or the app's own domain. |
| FR-076 | major | security | Server-side request forgery: requests / urlopen / httpx with a request-controlled URL | Build the URL server-side from a fixed allowlist of hosts or from frappe.conf; never let a request parameter choose the host or scheme. |
| FR-078 | major | apis | Controller class name does not match its DocType file | Name the controller class after the DocType: remove the spaces from the DocType name (Sales Order becomes SalesOrder) and use exactly that name in the file named after the DocType. |
| FR-079 | major | apis | Another document created or saved inside validate() / before_save() | Move document creation and saves of other documents to after_save, on_submit, or a background job; validate() and before_save() should only check and normalise this document. |
| FR-080 | major | performance | External HTTP call inside validate() / before_save() blocks every save | Fetch external data in a background job or cache it and read the cached value in validate(); never call the network from a lifecycle method that runs on every save. |
| FR-081 | minor | server-client | Synchronous frappe.call (async: false) freezes the Desk UI | Drop async: false and use the callback (or await frappe.xcall) — frappe.call is asynchronous by default and the UI stays responsive. |
| FR-082 | major | security | Secret value written to a log or print output | Log only that a secret is set or that an operation failed, never the secret itself; mask it first if it must appear in a message. |

## How to review

1. Read the code under review. For Server Scripts, also load
   `skills/frappe-server-scripts/SKILL.md`; for anything version-specific,
   also load `skills/frappe-upgrade/SKILL.md`.
2. Check every rule in the table above, in severity order: blockers first,
   then majors, then minors. For cross-file rules (FR-018, FR-019, FR-023,
   FR-056), read the whole submission — a JS file alone cannot prove server
   validation exists, and a helper in a file that was not submitted cannot
   be followed.
3. If a deterministic result is needed, run the Sensei checker (see below)
   and use its findings as the base — then add judgement the checker cannot
   make (is this `ignore_permissions` actually safe? is this redirect
   validated?).
4. Report findings as a table, ordered by severity:

   | Severity | File:line | Rule broken | Suggested fix |
   |---|---|---|---|
   | blocker / major / minor | ... | FR-0xx — title | ... |

   End with a one-line verdict: **approve**, **approve with fixes**, or
   **needs rework**. Verdict rule: any blocker → needs rework; else any
   major → approve with fixes; else approve. Also list the commands the
   user should run (`migrate`, `bench build --app <app>`, `clear-cache`)
   if the change needs them.

## Run the deterministic checker (optional, exact result)

The checker is a pure, dependency-free ES module. It reads code only —
it never runs it, sends it anywhere, or stores it. It needs Node only,
no `npm install`.

```bash
git clone https://github.com/irfanzahoor/frappe-sensei.git
cd frappe-sensei
node checker/checker.test.mjs   # runs the checker's own tests (236 pass)
```

Then, from any folder, run it on the files under review with a small
script (save as `review.mjs` next to the checkout):

```js
import { readFileSync } from 'node:fs';
import { checkFiles } from './frappe-sensei/checker/checker.js';
import rules from './frappe-sensei/rules/rules.json' with { type: 'json' };

const files = process.argv.slice(2).map((p) => ({ path: p, content: readFileSync(p, 'utf8') }));
const { findings, verdict, summary } = checkFiles(files, rules);
for (const f of findings) console.log(`${f.severity}  ${f.file}:${f.line}  ${f.ruleId} — ${f.rule}  Fix: ${f.fix}`);
console.log(`Verdict: ${verdict}  (${summary.blocker} blocker, ${summary.major} major, ${summary.minor} minor)`);
```

```bash
node review.mjs path/to/sales_order.py path/to/sales_order.js
```

Or skip the checkout entirely and use the live tool above — same rules,
same verdict.

## Known limitations (do not over-claim)

- The checker is regex/heuristic, not a full parser. A finding is a
  strong signal, not proof — read the flagged line before reporting it.
- **FR-023** (whitelisted method without a permission check): a permission
  check in a helper the method calls counts (up to 4 levels, across files
  in the same submission). A thin wrapper whose helper was not submitted
  still flags — check the whole app when a blocker looks wrong.
- **FR-056** (unresolvable dotted paths): needs the whole submission
  (`hooks.py` / `patches.txt` plus the module files they name).
- **FR-002 / FR-007**: path heuristics — they need real file paths
  (loose snippets with no app path cannot trigger them).
- **FR-018 / FR-019**: need both sides in the same submission (JS + its
  Python controller).
- Seed, demo, test and fixture data are exempt from the data rules
  (FR-030/FR-031/FR-063) by design — demo data is meant to be inserted.
- A line ending in `# noqa` suppresses all rules on that line.
- The checker does not know DocType JSON, so it cannot check field-level
  rules (for example `allow_on_submit` on money fields) — those need a
  human read of the DocType.

Grid Table — Native Emacs Table System with Plugins and Formulas
================================================================

[![Releases](https://img.shields.io/badge/Releases-Download-blue?logo=github)](https://github.com/KCNB/grid-table/releases)

https://github.com/KCNB/grid-table/releases

![Emacs logo](https://upload.wikimedia.org/wikipedia/commons/1/13/GNU_Emacs_Logo.svg) ![Table icon](https://upload.wikimedia.org/wikipedia/commons/thumb/3/3b/Icon-table.svg/512px-Icon-table.svg.png)

A native table system for Emacs that supports rich text, formula calculation, and plugin extensions. Grid Table implements a light-weight major mode, a formula engine, and a small plugin API. Use it to edit spreadsheets inside Emacs buffers, run calculations, and extend behavior with Emacs Lisp.

Badges
------
- [![Releases](https://img.shields.io/badge/Releases-Download-blue?logo=github)](https://github.com/KCNB/grid-table/releases)
- [![Emacs compatibility](https://img.shields.io/badge/Emacs-25%2B-brightgreen)](https://www.gnu.org/software/emacs/)
- [![License](https://img.shields.io/badge/License-MIT-yellowgreen)](LICENSE)

Quick links
-----------
- Release page: https://github.com/KCNB/grid-table/releases
- Source: this repository
- Issues: use GitHub issues in this repo

Download and run a release
--------------------------
Visit the release page at https://github.com/KCNB/grid-table/releases, download the release asset, and execute it in Emacs.

Typical steps:
1. Open the releases page: https://github.com/KCNB/grid-table/releases
2. Download the latest asset (for example: grid-table-1.2.3.el or grid-table-1.2.3.tar.gz).
3. In Emacs, run:
   - If the asset is a single .el file:
     - M-x load-file RET /path/to/grid-table-1.2.3.el RET
   - If the asset is a tarball:
     - Extract it and add the package folder to your load-path, then require the package:
       - (add-to-list 'load-path "~/.emacs.d/grid-table")
       - (require 'grid-table)

Install manually via package
----------------------------
- Copy grid-table.el into a directory in your Emacs load-path.
- Add this to your init file:
  - (require 'grid-table)
  - (grid-table-mode 1) ; optional: enable in current buffer

Features
--------
- Native Emacs major mode for grid editing.
- Rich text in cells (Emacs faces, links, and text properties).
- Cell formulas with references (A1-style and named ranges).
- Built-in formula functions: SUM, AVG, MIN, MAX, IF, CONCAT, DATE, and more.
- Plugin API for cell renderers, validators, and import/export filters.
- Keyboard-focused workflow for fast edits.
- CSV and Org table import/export.

Core concepts
-------------
- Grid buffer: a buffer in grid-table-mode.
- Cell: a single grid unit, addressable by column letter and row number (A1, B2).
- Formula: a cell value starting with '='; grid-table evaluates formulas and caches results.
- Plugin: an Emacs Lisp object that extends behavior by registering hooks or handlers.

Basic usage
-----------
Create a new grid buffer:
- M-x grid-table-new RET
- Or open a file with .grid extension and grid-table-mode starts.

Navigate cells:
- C-c C-n — next cell (right)
- C-c C-p — previous cell (left)
- C-c C-d — down
- C-c C-u — up
- TAB and S-TAB also move between editable cells

Edit a cell:
- RET to enter edit mode for the current cell.
- Type rich content. You can use Emacs faces, links, and images.
- To enter a formula, start the cell with "=". Example:
  =SUM(A1:A3)
- After edit, press RET to commit. Grid Table evaluates formulas and updates dependents.

Example table (text)
- Open a grid buffer and paste:
  | A      | B      | C       |
  |--------|--------|---------|
  | 10     | 20     | =A1+B1  |
  | 3      | 4      | =SUM(A2:B2) |
  | =SUM(A1:A2) | =AVG(B1:B2) | Total |

Formula engine
--------------
Grid Table uses a simple formula parser with these features:
- A1-style references: A1, B2, C10.
- Ranges: A1:A3, B1:D4.
- Functions: SUM(range), AVG(range), MIN, MAX, IF(cond, then, else), CONCAT.
- Named ranges: define with M-x grid-table-define-range RET NAME START END RET
- Lazy evaluation with memoization. The engine detects cycles and reports errors in the cell.

Examples:
- =SUM(A1:A5)
- =IF(A1>10, "high", "low")
- =CONCAT(A1, " / ", B1)
- =DATE(2025, 8, 15)

Plugin system
-------------
Grid Table ships with a small plugin API. Plugins can:
- Render cells differently (colors, icons).
- Validate input on save.
- Provide import/export filters (CSV, JSON, Org).
- Add custom formula functions.

How to write a plugin (minimal example):
- Create a file my-grid-plugin.el:
  (defun my-grid-validate-price (cell-value)
    "Validate price values are non-negative."
    (if (and (numberp cell-value) (< cell-value 0))
        (grid-table-cell-error "Price must be non-negative")
      cell-value))

  (grid-table-register-validator 'my-price-validator
                                :columns '(C)
                                :fn #'my-grid-validate-price)

- Then load the plugin and enable it:
  (load-file "/path/to/my-grid-plugin.el")

Built-in plugins
----------------
- csv-import: read CSV into a grid with proper typing.
- org-export: send grid to an Org table block.
- latex-export: export grid as LaTeX tabular.
- autosave: save grid state to a file on idle.

Import and export
-----------------
CSV import:
- M-x grid-table-import-csv RET /path/to/file.csv RET

Export to CSV:
- M-x grid-table-export-csv RET /path/to/out.csv RET

Export to Org:
- M-x grid-table-export-org RET /path/to/out.org RET

Keybindings reference
---------------------
- M-x grid-table-new — create new grid buffer
- M-x grid-table-open — open grid file
- C-c C-n — move right
- C-c C-p — move left
- C-c C-d — move down
- C-c C-u — move up
- RET — edit cell
- C-c C-c — commit cell edit
- C-c C-e — evaluate formulas in buffer
- C-c C-s — save grid
- C-c C-i — import
- C-c C-x — export

Configuration
-------------
Place configuration in your init file:
- Set default number format:
  (setq grid-table-default-number-format "%.2f")
- Customize keybindings:
  (define-key grid-table-mode-map (kbd "C-c C-n") #'grid-table-next-cell)
- Enable autosave plugin by default:
  (require 'grid-table-autosave)
  (grid-table-autosave-enable)

Development
-----------
- Clone the repo.
- Run tests:
  - emacs -Q --batch -l ert -l test/grid-table-test.el -f ert-run-tests-batch-and-exit
- Code style:
  - Use Emacs Lisp style guide.
  - Keep functions small and well-documented.
- Add a plugin in plugins/ and register it in plugins/README.md.

Examples and screenshots
------------------------
Screenshot examples will show the mode, formulas, and a plugin rendering. Use your own screenshot images in the docs. The mode renders formulas inline and shows a small formula bar at the top of the buffer.

API reference (selected)
------------------------
- grid-table-new (&optional rows cols) -> buffer
- grid-table-open (file) -> buffer
- grid-table-save (&optional file) -> t
- grid-table-get-cell (addr) -> value
- grid-table-set-cell (addr value) -> t
- grid-table-evaluate-buffer -> t
- grid-table-register-validator (name &rest opts)
- grid-table-register-renderer (name &rest opts)

Troubleshooting
---------------
- If a formula shows #ERR, check for cycles or bad references.
- If a plugin fails to load, ensure it adds itself to load-path and uses grid-table-register-* APIs.
- If images or faces do not show in cells, confirm Emacs supports images and faces in your terminal or GUI.

Contributing
------------
- Open an issue for bugs or feature requests.
- Fork the repository and create a branch per feature.
- Write tests for new features.
- Submit a pull request with a clear description and changelog entry.

License
-------
This project uses the MIT License. See LICENSE for details.

Contact and links
-----------------
- Issues and PRs: use the GitHub repo issue tracker.
- Release page (downloadable assets and release notes):
  https://github.com/KCNB/grid-table/releases

  Download the appropriate asset from the releases page and execute it in Emacs as described above.
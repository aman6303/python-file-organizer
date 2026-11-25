# python-file-organizer

A production-grade, modular, and safe Python CLI tool for automating directory cleanup, smart file categorization, duplicate detection, and real-time folder watching.

Prefer a zero-dependency approach (pure Python standard library: argparse + curses + hashlib)

All destructive operations (moving or trashing files) will default to requiring confirmation unless --yes / -y is passed. Furthermore, duplicate trashing will use the OS trash bin (send2trash) instead of permanent disk removal (os.remove).


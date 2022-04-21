# Blackd for PyCharm

If you:

- Use PyCharm
- Use [black](https://github.com/psf/black) to format your code
- Want PyCharm to format your code when you save
- And wish it was fast to do so

Read on...

# Quick setup

First, run the `blackd` container that this repository provides:

    $ git clone git@github.com:nigelmcnie/black.git
    $ cd black
    $ docker-compose up

Then install [BlackConnect](https://plugins.jetbrains.com/plugin/14321-blackconnect),
and restart PyCharm.

Now you can configure BlackConnect in PyCharm's settings:

- The "Check connection" button should work: the container is listening on the
  default port for `blackd`.
- "Trigger when saving changed files" lets you write your code, save the file,
  and have `blackd` reformat it in front of you - which can be advantageous
  when working out how to split up long strings, and so on. Sometimes people
  find this behaviour annoying, and that can be because `black` was slow for them
  previously - now you're using `blackd`, consider trying it again.
- "Load from `pyproject.toml`" is handy for projects that configure `black`
  that way.

Once set up, you can try it by (for example) putting a few blank lines into
your file, then saving it. If you're happy, I suggest just running the
container in the background - kill the `docker-compose up` command and then
re-run with `docker-compose up -d`. Consider also making it run when you log
in, so you can forget about it from now on.

# Theory

You're already sold on using `black`, it's just a question of how. Do you:

- Run it manually on the CLI when it suits you?
- Run it as part of `pre-commit`?
- Have a keybinding in your editor?
- Have it run when you save a file?

Each option is about moving the reformatting of files closer to the time that
you actually change them. This has tradeoffs. Observing the result more often
lets you react to any changes it makes faster, and I honestly find it an easier
reading experience. However, running `black` more can cause sync conflicts in
your editor, especially if it takes a little while to run - and it can be
costly to reformat all your open files in some cases, like changing branches.
The problems get worse the larger the file is, and while a 4,000 line file is a
code smell, you don't always get to choose the code you work with.

This little docker container essentially aims to cut the cost of the formatting
operation, so you feel more confident to run it more often. It does this by
[using `blackd`, which exposes `black`'s functionality over
HTTP](https://black.readthedocs.io/en/stable/usage_and_configuration/black_as_a_server.html).
That means that each formatting operation is an HTTP request to a service that
already paid the python startup cost, so you don't need to constantly pay it
each time a file needs reformatting.

After that, all you need is some way to tell PyCharm to use it, and that's what
[BlackConnect](https://plugins.jetbrains.com/plugin/14321-blackconnect) is for.
I'm not aware of such functionality for VSCode or other IDEs, but it might
exist all the same.

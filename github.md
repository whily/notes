Notes for Github/git.

### SSH keys

Just
follow [Github's guide on SSH](https://help.github.com/articles/generating-an-ssh-key/).
Then
[change the remote's URL from https to ssh](https://help.github.com/articles/changing-a-remote-s-url/).

### Handle miscellaneous errors

#### Handle error `Your local changes to the following files would be overwritten by merge` when running `git pull origin master`

To keep the local changes:

``` shell
git stash
git pull origin master
git stash pop
```

### Markdown

#### HTML code

* To show pipe '|' character in a table (where '|' serves as column
  separation), use HTML code: &amp;#124;

* Similarly, to literally show () after [], HTML code &amp;#40; can be
  used for the left parenthesis '(' (of course other special symbols
  can be substituted in this case).

* To include a literal backtick (`` ` ``) inside a backtick code block, one can use
  multiple backticks as the beginning and closing delimiters, and add spaces if needed.
  For example, by using ``` ``this is a back tick ` `` ``` can result in ``this is a back tick ` ``.

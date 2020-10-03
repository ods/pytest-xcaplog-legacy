# Extended log capture fixture

## Disclaimer

It's legacy version.  The idea works (to some extent) with pytest prior to 6th
major version.  Starting from `6.*` pytest uses [separate handlers for caplog
and reports](https://github.com/pytest-dev/pytest/pull/7434).

## Usage example

With standard `caplog` fixture it usually looks like:

    def test_smth(caplog):
        with caplog.at_level(logging.ERROR):
            code_that_logs()
        assert any(
            rec.name.startwith('mymodule') and
                re.search(pattern, rec.getMessage())
            for rec in caplog.records
        )
        # Here is the problem: it can remove other records, that might be
        # useful for debugging
        caplog.clear()

With `xcaplog` it's simpler and safer:

    def test_smth(xcaplog):
        with xcaplog.at_level(logging.ERROR):
            code_that_logs()
        recs = xcaplog.pop_matching(name='mymodule', message=pattern)
        assert len(recs) == 1

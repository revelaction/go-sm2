## go-srs

`go-srs` provides a serie of libraries to easily build [spaced repetition
learning](https://en.wikipedia.org/wiki/Spaced_repetition) capable programs and
servers.

It currently provides: 

- An implementation of the [SuperMemo 2](https://www.supermemo.com/english/ol/sm2.htm) (sm2) algorithm.
- A local db client based on [badger](github.com/outcaste-io/badger).
- A unique id generator based on [ulid](github.com/oklog/ulid).

## Use

First instantiate the srs struct with the provided algorithm, uid and db implementations:

	// Algo
	now := time.Now().UTC()
	sm2 := sm2.New(now)

	// Db
	opts := badger.DefaultOptions("./badger")
	opts.Logger = nil
	bad, err := badger.Open(opts)
	defer bad.Close()
	db := bdg.New(bad, sm2)

    // uid
	entropy := ulidPkg.Monotonic(crand.Reader, 0)
	uid := ulid.New(entropy)

    // instantiate srs struct
	hdl := srs.New(db, uid)


After that, you can update and save card reviews of a given deck:

	r = review.Review{}
	r.DeckId = 'somedeckid'

	r.Items = []review.ReviewItem{
		{CardId: 65, Quality: 1},
		{CardId: 3, Quality: 5},
		{CardId: 1, Quality: 0},
	}

	_, err = hdl.Update(r)
	if err != nil {
		fatal(err)
	}


And retrieve cards that are due for review

	tdue := time.Now().UTC()
	dueCards, _ := hdl.Due(res.DeckId, tdue)


See `srs_test.go` for more examples.

## Additional implementations

`go-srs` provides interfaces for [db](db/db.go), [algorithm](algo/algo.go) and
[unique id](uid/uid.go) implementations.

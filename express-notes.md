# Express Notes

# Contents
+ Extracting data from either URI or request body
.. req.params
.. req.query
.. req.body
+


# Extracting data from either URI or request body

## req.params

```js
router.get("/:urlTitle", (req, res, next) => {
    req.params.urlTitle
});
```
## req.query
Also is used when doing a form with method `GET`

```js
// /search?tag=foo
router.get("/search", (req, res, next) => {
    req.query.tag
});
```

## req.body
Used when doing a form with method `POST` and body-parser middleware

```js
router.post("/", (req, res, next) => {
    name: req.body.name,
    email: req.body.email
});
```

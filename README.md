eweb
====

An AJAX web framework for Erlang (and YAWS) allowing easier development of web applications.

Features:

* Easily embed anonymous functions in EHTML that are executed on the server when triggered by the client.
* Generating and writing Javascript through Erlang tuples and lists.
* Web application testing in Erlang (experimental).
* Control the execution of JS before, during and after asynchronous server communication.
* Simple and reliable transport of browser and form data for server side processing.
* Write entire web applications (except CSS, which is 'coming soon') in a single, unified language.

## Examples ##

The following sample page provides the user with a button that has had an anonymous function attached to it on the server. When the user presses the button, the function is executed and the `output` div displays 'Hello, World!'.

```
<erl>

out(_) ->
	{ehtml,
		{html, [],
			[
				{head, [],
					[
						{script, [{type, "text/javascript"}], [ejs:eval(web_ajax:base())]},
						{title, [], ["eWeb Example 1"]}
					]
				},
				{body, [],
					[
						{button,
							[
								{onClick,
									web_ajax:request(fun() -> {to, "output", "Hello, World!"} end)
								}
							],
							["Say Hello"]
						},
						{'div', [{id, "output"}]}
					]
				}
			]
		}
	}.

</erl>
```

The following example provides an as-you-type filtering of a list of fruits. The filtering is done on the server, without the client having explicit access to the dataset.

```
<erl>

out(_) ->
	{ehtml,
		{html, [],
			[
				{head, [],
					[
						{script, [{type, "text/javascript"}], [ejs:eval(web_ajax:base())]},
						{title, [], ["eWeb Example 5"]}
					]
				},
				{body, [],
					[
						{input,
							[
								{id, "search"},
								{onKeyUp,
									web_ajax:request(
										fun([Search]) -> {to, "output", format_list(filter(Search))} end,
										[{send, ["search"]}]
									)
								}
							]
						}, {br},
						{'div', [{id, "output"}], [format_list(list())]}
					]
				}
			]
		}
	}.

filter("") -> list();
filter(Str) -> lists:filter(fun(X) -> string:str(X, Str) =/= 0 end, list()).

format_list(L) ->
	{'div', [],
		[
			{b, [], [integer_to_list(length(L)) ++ " results found."]}
		] ++
		[ {p, [], [X]} || X <- L ]
	}.

list() -> ["apples", "oranges", "pears", "banannas", "pineapple"].

</erl>
```

## Usage ##

- Build eweb and make sure that your project has the modules loaded.
- Add `web\_ajax\_server:start()` to your application's startup routine.
- Add `{script, [{type, "text/javascript"}], [ejs:eval(web\_ajax:base())]}` to the head tag of each page that is going to perform AJAX requests.
- Use `web\_ajax:request` to produce anonymous JS functions that contact the server when executed. These functions can be used as action handelers for form elements (for example, `onClick`).

## Future Work ##

* Documentation!
* Tests!
* Better packaging.
* Support for CSS, so that _all_ elements of the web application can be written in Erlang.
* Web sockets support.

## Interviewing
Notes and questions to consider when interview a Software Development candidate

Ask questions that reveal candidates: 
- Tech passion
- Recent side projects 
- Something they're interested in learning 
- *"Teach me something I don't know"*
    - Could be something their interested in or did recently at work. 
    - *Good for candidates interviewing for a lead role*
- What lead them to the company
    - How interested are they in the product or tech stack. 
    - *weed out those desperate for a job*

`should show interest in learning new things`


Intern/Junior - learn about long term aspirations

Remote position - find a history of remote proficiency

# Engineering

1. In your opinion, what is the most import attribute that makes up quality code?
    - Answers may include, test and code coverage, documentation, performance, observability, or the ever popular "it works"
    - Do they have any tools or processes that enforce their chosen attribute?
1. Can you tell me about a project or idea that you championed in your team or organization? This could be a new service or feature, a refactor or migration, or a change in process.
    - How did you concieve of the idea?
    - What steps did you take to bring this idea to life?
    - What challenges did you face along the way?
    - Looking for initiative and the ability to see a problem and work towards a solution. Something more than someone who just knocks out Jira tickets.
1. I need to programmatically trigger a long running bash script. How would you build something that solves this problem.
    - assume the script is a piece of ancient legacy code that needs to be ran at a non regular intervals

1. Explain the process you would take to migrate a service from one database to another.
    - assume the service does reads/writes to the database
    - does the candidates solution transition smoothly? 
    - does it require downtime and a cut over period?
    - do they mention abstracting the data interface
    - does their solution handle the data migration/back filling of the data.

1. Why does Netflix deliberately take down its own servers? How would you approach doing the same?
    - Netflix's [Chaos Monkey](https://netflix.github.io/chaosmonkey/) deliberately takes down instances to ensure they system is resilient and fault tolerant.
    - Whether they build their own service or deploy `Chaos Monkey`, what considerations do they make when deciding what to take down? 
        - specific times
        - frequency of termination
        - staging or production
        - avoid critical services or single points of failure

1. What is your _least_ favorite thing about your _favorite_ programming language?
    - Use this to try and gauge technical competency for something they should be experts in.
        examples for me with Go:
		- iterating over a map is not deterministic.
 		- profiling can be difficult to understand and use.
  		- Going to version 2 of modules can be tricky.
 

# Design

Ask the interviewee what's something they're interested in outside of work (sports, food, movies, music, D&D, camping, board games, etc...) then explain that, together, we are going to walk through designing a product that is built around their chosen topic.

## Interface
1. What would would you build to interface with the system?
    - `REST API`
        - Ask them to describe some of the APIs and endpoints they would need to build
            - expect the HTTP verb, path, and maybe rough request/response for each endpoint.
            - HTTP verbs should align close with:
                - `GET` = retrieval
                - `POST` = create (or possibly an action)
                - `PUT` = update
                - `PATCH` = update (explain what differences there are between PUT)
                - `DELETE` = delete
            - How would they maintain and distribute the API specification?
        - `API Gateway`
            - Would you use an API Gateway for your services?
                - What are the benefits of an API Gateway? 

2. How will users access the platform?
    - `Authentication/Authorization`
        - What would use for auth? (plaintext passwords in a database, or an authentication service like 0Auth)

## Deployment
1. What dependencies are there for this service?
    - expect at least a database layer and them to describe what endpoints depend on that dependency
    - maybe they add more complex dependencies that require caching or search functionality
2. How will these dependent services, as well as the applications, be deployed
    - `Cloud`
        - What cloud platforms do they have experience with
    - `Kubernetes`
        - Ask for the benefits or use cases of Kubernetes
        - Are there any specific tools that you would use to aid in your deployments, whether infrastructure of application
            - _helm_, _terraform_

## Performance
1. Assuming that the platform is deployed but is experiencing slowness and failures are being reported. What are things you would do to combat slow and failed responses?
    - `Monitoring`
        - What would you set up for monitoring your system
            - _logging_, _metrics_, _alerting_
        - `Application Performance Metrics`
            - What would you set up to collect metrics for the application?
                - tracing, openmetrics, some stats agent, etc...
            - What are the pros and cons of tracing when it comes to Application Performance Metrics.
                - answer should include gathering the rate/duration of tasks
                - may include discovering errors or particularly slow parts of the application
                - maybe you trace too much and it's expensive
                    - rate limit your traces.
                - maybe you over trace everything and have traces with >10k spans and slows things down or causes memory issues
                    - limit what you actually trace
    - `Scaling`
        - What tools or technologies would you leverage to improve the health of your service at scale?
            - _load balancing_, _auto scaling_
        - What is right sizing a pod?
        - Why wouldn't you want one large instance that is able to handle your peak traffic
    - `Optimization`
        - What would you do if your metrics pointed to frequently slow DB queries?
            - _caching_, _query optimization_

# GO

1. What do you love about `GO`? *my answers as example*
    - Readability
        - Clear, statically typed, language
        - Easy to follow code 
        - Easy to dive into packages, no magic dependency injection
        - All GO code all looks similar 
    - Testability
        - Easy to test. Well written code is not difficult to target 100% test coverage
        - `_test.go` files live next to code within the package
            - Easy to find tests
            - No need to hack around access modifiers
    - Dependability
        - Compiled language. `go build` and run anywhere.
        - Cloud focused. 
            - A binary easy to put in and run in a container and run in the cloud
        - Backward compatibility promise from v1
        - Tooling is easy and clear, from the GO cli to managed linters
            - `go build`, `go mod`, `go test`, `go gen`, etc
    - Usability
        - Iterfaces through "duck typing" makes undestanding abstractions easy
        - Embeded structs and interfaces provide everything you need from OOP, no inheritance necessary
    - Concurrency
        - First class support for concurrency using go routines and channels
        - Lightweight threading built in from the ground up

2. Write a function that joins string slices from a struct. [play.golang](https://go.dev/play/p/VRS4vJhlFU8)
    ``` 
    func targets(A, B Report) []string {
	    return append(A.Targets, B.Targets...)
    }
    ```
    `append` should be expected to be used initially as it should be commonly used for a go developer. But maybe they see past this this and iterate over both slices and insert the values into a slice the length of both input values. 
    1. Add a duplicate value into one of the Report structs and ask them to handle removing duplicate values from the result
    ```
    func targets(reports ...Report) []string {
        index := map[string]struct{}{}
        var results []string
        for _, report := range reports {
            for _, target := range report.Targets {
                if _, ok := index[target]; !ok {
                    results = append(results, target)
                    index[target] = struct{}{}
                }
            }
        }
        return results
    }
    ```
    whether in a new function or not, they should dedupe the targets

    - Using a map would be expected for de duping
    - A map of structs is memory efficient since an empty struct takes no memory, but a map[string]bool would work just as well
    - Watch for assignments to nil map
    2. Ask them to change the function so that it would work for any number of `Report` structs
    - The example above already includes this by using a variadic parameter

3. How would you write a struct as JSON to a file? [play.golang](https://go.dev/play/p/S5isio_j_Jr)

    being able to use `json.Marshal(v interface{})` is something that should be familiar to any GO developer. Remembering how to write to a file might be need some assistance. Let the candidate know that they can ask for any interface that they may need to know but don't know off the top of their head because play.golang has no intellisense.
    - `encoding/json` interfaces
        - `func Marshal(v interface{}) ([]byte, error)`
        - `func Unmarshal(data []byte, v interface{}) error`
        - `func NewEncoder(w io.Writer) *Encoder`
            - `func (enc *Encoder) Encode(v interface{}) error`
        - `func NewDecoder(r io.Reader) *Decoder`
            - `func (dec *Decoder) Decode(v interface{}) error`    
    - `*os.File` or `io.Writer` interfaces
        - `func Write(p []byte) (n int, err error)`

    This can be done in one line with `json.NewEncoder(f).Encode(&t)` but they might instead do:
    ```
    func write(f *os.File, finalReport Report) error {
        b, err := json.Marshal(&finalReport)
        if err != nil {
            return err
        }

        _, err = f.Write(b)
        if err != nil {
            return err
        }
        return nil
    }
    ```
    They should come up with a solution that marshals the struct to bytes and writes the bytes to the file
    - Be sure they handle errors
    - Whether they ignore the int returned from Write() or not, ask them if they know what they could use that value for
    
    When they're finished, ask them how they could change the function so that it was able to write to an `http.ResponseWriter` 
    - Hopefully they do more than change *os.File to `http.ResponseWriter`
    - They should change the parameter to an `io.Writer` and explain that it would allow for anything that implements the io.Writer interface to be accepted

    Ask them how they could change the function to support any struct instead of just the `Thing` struct
    - The should explain that they could turn the `v Thing` parameter into `v interface{}`
    - They would have to change the function call to use a pointer for this to work, make sure they know this

    Ask them how they could change the code if they wanted the fields to be written with `camelCase` fields
    - They should add `json:"name"` tags to the struct field


4. How would you make multiple http calls at the same time? [play.golang](https://go.dev/play/p/QadebQBuFYy)
    - The approach for this might vary a lot. They could use some variation of channels and go routines. Possibly the `sync.WaitGroup` or an `errgroup.Group`

    How would they collect the response data from the calls made?
    - They should explain how they would create a channel to send the response data to.

    How would they change their solution if they didn't need the response data, but only need to know that they calls were successful?
    - They could still use channels for this, maybe an error channel, but I would use an `errgroup.Group`

5. What is `context.Context` and how is it used?
    - context is for cancellation, for passing along into goroutines to handle timeouts or application cancellation.
    - context is for request scoped variables. A dangerous catch all bag for storing key/value pair values in the scope of context.
        - If they did not mention that values should be specific to the request scope, or that one should perform a high level of caution before throwing values into context, ask them what kinds of variables would they put in context.


6. What are channels used for in GO?
    - Channels are used for communication between go routines. They are used to pass data between go routines or synchronize data in a thread safe manner.
    1. What is the difference between a buffered and unbuffered channel?
    - channels are unbuffered by default.
    - unbuffered channels block while waiting for a receiver to read from the channel.
    - a buffer limit can be set on a channel that will block sends to a channel if the buffer is full, until the application has read from the channel to free up the buffer.

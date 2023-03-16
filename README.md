Intermediate API in R
================
Hedia Tnani

# Intermediate API in R

## 1. Basic API in R

Lat time we saw how to make a `GET` request to retrieve data from an
API. We’ll remember again the different steps. We’ll also add query
parameters and authentication.

Let’s remember what we did last time using the Ensembl Rest API.

First we prepare the URL.

``` r
library(httr) # accessing APIs
# Preparing the URL 
baseURL <- "https://rest.ensembl.org"
ext <- "/regulatory/species/homo_sapiens/microarray?"
URL <- modify_url(baseURL, path = ext)
```

As we saw last time a **Uniform Resource Identifier (URI)** **resource**
specifies which resource you want to access. A URI is a generalization
of a URL (Uniform Resource Locator)—what you commonly think of as "web
addresses".

Then we query the database.

``` r
response = GET(URL)
response
```

    Response [https://rest.ensembl.org/regulatory/species/homo_sapiens/microarray]
      Date: 2023-03-16 14:44
      Status: 200
      Content-Type: application/json
      Size: 3.46 kB

Then we convert the JSON format to a dataframe by using the `fromJSON`
function.

``` r
library(jsonlite)
fromJSON(content(response,as = "text", encoding = "UTF-8" ))
```

        type      format                    array   vendor
    1  OLIGO  EXPRESSION                 OneArray  PHALANX
    2  OLIGO  EXPRESSION                 CODELINK CODELINK
    3  OLIGO  EXPRESSION             HumanWG_6_V3 ILLUMINA
    4  OLIGO  EXPRESSION                  GPL6848  AGILENT
    5  OLIGO METHYLATION      HumanMethylation450 ILLUMINA
    6  OLIGO  EXPRESSION            HumanRef-8_V3 ILLUMINA
    7  OLIGO  EXPRESSION              WholeGenome  AGILENT
    8  OLIGO         CGH                  CGH_44b  AGILENT
    9  OLIGO  EXPRESSION    SurePrint_G3_GE_8x60k  AGILENT
    10 OLIGO  EXPRESSION                  HC-G110     AFFY
    11 OLIGO  EXPRESSION SurePrint_G3_GE_8x60k_v2  AGILENT
    12 OLIGO  EXPRESSION                 HG-Focus     AFFY
    13 OLIGO  EXPRESSION     WholeGenome_4x44k_v1  AGILENT
    14 OLIGO  EXPRESSION     WholeGenome_4x44k_v2  AGILENT
    15 OLIGO  EXPRESSION                 GPL26966  AGILENT
    16 OLIGO  EXPRESSION               HG-U133A_2     AFFY
    17 OLIGO  EXPRESSION                 GPL19072  AGILENT
    18 OLIGO METHYLATION       HumanMethylation27 ILLUMINA
    19 OLIGO  EXPRESSION                 HG-U133B     AFFY
    20 OLIGO  EXPRESSION                  HTA-2_0     AFFY
    21 OLIGO  EXPRESSION           HG-U133_Plus_2     AFFY
    22 OLIGO  EXPRESSION                HG_U95Av2     AFFY
    23 OLIGO  EXPRESSION                  HG-U95B     AFFY
    24 OLIGO  EXPRESSION                  HG-U95C     AFFY
    25 OLIGO  EXPRESSION                  HG-U95D     AFFY
    26 OLIGO  EXPRESSION                  HG-U95E     AFFY
    27 OLIGO  EXPRESSION                  HG_U95A     AFFY
    28 OLIGO  EXPRESSION                 HuGeneFL     AFFY
    29 OLIGO  EXPRESSION                 U133_X3P     AFFY
    30 OLIGO  EXPRESSION                PrimeView     AFFY
    31 OLIGO  EXPRESSION       HT_HG-U133_Plus_PM     AFFY
    32 OLIGO  EXPRESSION           HuEx-1_0-st-v2     AFFY
    33 OLIGO  EXPRESSION         HuGene-2_0-st-v1     AFFY
    34 OLIGO  EXPRESSION         HuGene-1_0-st-v1     AFFY
    35 OLIGO  EXPRESSION         HuGene-2_1-st-v1     AFFY
                         description
    1                           <NA>
    2                           <NA>
    3                           <NA>
    4                           <NA>
    5                           <NA>
    6                           <NA>
    7                           <NA>
    8                           <NA>
    9                           <NA>
    10                          <NA>
    11                          <NA>
    12                          <NA>
    13                          <NA>
    14                          <NA>
    15                          <NA>
    16                          <NA>
    17                          <NA>
    18                          <NA>
    19                          <NA>
    20 Human Transcriptome Array 2.0
    21                          <NA>
    22                          <NA>
    23                          <NA>
    24                          <NA>
    25                          <NA>
    26                          <NA>
    27                          <NA>
    28                          <NA>
    29                          <NA>
    30                          <NA>
    31                          <NA>
    32                          <NA>
    33                          <NA>
    34                          <NA>
    35                          <NA>

Then we saw how to make a function.

``` r
library(httr)
library(jsonlite)

get_microarrays <- function(baseURL, ext, content_type){

    r <- GET(paste(baseURL, ext, sep = ""), accept(content_type))

    stop_for_status(r)

    if (content_type == 'application/json'){
        return (fromJSON(content(r, "text", encoding = "UTF-8")))
    } else {
        return (content(r, "text", encoding = "UTF-8"))
    }
}

baseURL <- "https://rest.ensembl.org"
ext <- "/regulatory/species/homo_sapiens/microarray?"
con <- "application/json"


get_microarrays(baseURL, ext, con)
```

                         description                    array  type      format
    1                           <NA>                 OneArray OLIGO  EXPRESSION
    2                           <NA>                 CODELINK OLIGO  EXPRESSION
    3                           <NA>             HumanWG_6_V3 OLIGO  EXPRESSION
    4                           <NA>                  GPL6848 OLIGO  EXPRESSION
    5                           <NA>      HumanMethylation450 OLIGO METHYLATION
    6                           <NA>            HumanRef-8_V3 OLIGO  EXPRESSION
    7                           <NA>              WholeGenome OLIGO  EXPRESSION
    8                           <NA>                  CGH_44b OLIGO         CGH
    9                           <NA>    SurePrint_G3_GE_8x60k OLIGO  EXPRESSION
    10                          <NA>                  HC-G110 OLIGO  EXPRESSION
    11                          <NA> SurePrint_G3_GE_8x60k_v2 OLIGO  EXPRESSION
    12                          <NA>                 HG-Focus OLIGO  EXPRESSION
    13                          <NA>     WholeGenome_4x44k_v1 OLIGO  EXPRESSION
    14                          <NA>     WholeGenome_4x44k_v2 OLIGO  EXPRESSION
    15                          <NA>                 GPL26966 OLIGO  EXPRESSION
    16                          <NA>               HG-U133A_2 OLIGO  EXPRESSION
    17                          <NA>                 GPL19072 OLIGO  EXPRESSION
    18                          <NA>       HumanMethylation27 OLIGO METHYLATION
    19                          <NA>                 HG-U133B OLIGO  EXPRESSION
    20 Human Transcriptome Array 2.0                  HTA-2_0 OLIGO  EXPRESSION
    21                          <NA>           HG-U133_Plus_2 OLIGO  EXPRESSION
    22                          <NA>                HG_U95Av2 OLIGO  EXPRESSION
    23                          <NA>                  HG-U95B OLIGO  EXPRESSION
    24                          <NA>                  HG-U95C OLIGO  EXPRESSION
    25                          <NA>                  HG-U95D OLIGO  EXPRESSION
    26                          <NA>                  HG-U95E OLIGO  EXPRESSION
    27                          <NA>                  HG_U95A OLIGO  EXPRESSION
    28                          <NA>                 HuGeneFL OLIGO  EXPRESSION
    29                          <NA>                 U133_X3P OLIGO  EXPRESSION
    30                          <NA>                PrimeView OLIGO  EXPRESSION
    31                          <NA>       HT_HG-U133_Plus_PM OLIGO  EXPRESSION
    32                          <NA>           HuEx-1_0-st-v2 OLIGO  EXPRESSION
    33                          <NA>         HuGene-2_0-st-v1 OLIGO  EXPRESSION
    34                          <NA>         HuGene-1_0-st-v1 OLIGO  EXPRESSION
    35                          <NA>         HuGene-2_1-st-v1 OLIGO  EXPRESSION
         vendor
    1   PHALANX
    2  CODELINK
    3  ILLUMINA
    4   AGILENT
    5  ILLUMINA
    6  ILLUMINA
    7   AGILENT
    8   AGILENT
    9   AGILENT
    10     AFFY
    11  AGILENT
    12     AFFY
    13  AGILENT
    14  AGILENT
    15  AGILENT
    16     AFFY
    17  AGILENT
    18 ILLUMINA
    19     AFFY
    20     AFFY
    21     AFFY
    22     AFFY
    23     AFFY
    24     AFFY
    25     AFFY
    26     AFFY
    27     AFFY
    28     AFFY
    29     AFFY
    30     AFFY
    31     AFFY
    32     AFFY
    33     AFFY
    34     AFFY
    35     AFFY

## 2. Intermediate API in R

### **2.1. Adding Query Parameters**

What if we need to add query parameters ?

Query parameters are the extra **parameters** (arguments) about what
resource to access.

We can pass `query` parameters as a named list, where the names are the
`API` fields for each query parameter.

We can use the `query` parameter of the **`GET()` function** from the
`httr` package to add the query parameters to our request.

So let’s edit the previous `get_microarrays()` function.

-   Add a **new parameter** named `queries` to the function and assign
    `list()` to it to make it an optional parameter: `queries = list()`.
    We assign a `list()` here so that multiple optional parameters can
    be passed to our function.

-   Edit the `GET()` function to set its `query` parameter to our
    function parameter `queries`.

Let’s take again the same example using the Ensembl rest API.

``` r
library(httr)
library(jsonlite)

get_microarrays <- function(baseURL, endpoint, queries){

    r <- GET(paste(baseURL, endpoint, sep = ""), queries)

    stop_for_status(r)

    return (content(r, "text", encoding = "UTF-8"))
    }

baseURL <- "https://rest.ensembl.org"
endpoint <- "/regulatory/species/homo_sapiens/microarray?"
queries = list(format="json")


get_microarrays(baseURL, endpoint, queries)
```

    [1] "[{\"format\":\"EXPRESSION\",\"description\":null,\"array\":\"OneArray\",\"type\":\"OLIGO\",\"vendor\":\"PHALANX\"},{\"type\":\"OLIGO\",\"vendor\":\"CODELINK\",\"description\":null,\"array\":\"CODELINK\",\"format\":\"EXPRESSION\"},{\"description\":null,\"array\":\"HumanWG_6_V3\",\"format\":\"EXPRESSION\",\"vendor\":\"ILLUMINA\",\"type\":\"OLIGO\"},{\"vendor\":\"AGILENT\",\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"description\":null,\"array\":\"GPL6848\"},{\"description\":null,\"array\":\"HumanMethylation450\",\"format\":\"METHYLATION\",\"type\":\"OLIGO\",\"vendor\":\"ILLUMINA\"},{\"format\":\"EXPRESSION\",\"description\":null,\"array\":\"HumanRef-8_V3\",\"vendor\":\"ILLUMINA\",\"type\":\"OLIGO\"},{\"description\":null,\"array\":\"WholeGenome\",\"format\":\"EXPRESSION\",\"type\":\"OLIGO\",\"vendor\":\"AGILENT\"},{\"vendor\":\"AGILENT\",\"type\":\"OLIGO\",\"format\":\"CGH\",\"description\":null,\"array\":\"CGH_44b\"},{\"format\":\"EXPRESSION\",\"array\":\"SurePrint_G3_GE_8x60k\",\"description\":null,\"type\":\"OLIGO\",\"vendor\":\"AGILENT\"},{\"array\":\"HC-G110\",\"description\":null,\"format\":\"EXPRESSION\",\"type\":\"OLIGO\",\"vendor\":\"AFFY\"},{\"vendor\":\"AGILENT\",\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"description\":null,\"array\":\"SurePrint_G3_GE_8x60k_v2\"},{\"description\":null,\"array\":\"HG-Focus\",\"format\":\"EXPRESSION\",\"type\":\"OLIGO\",\"vendor\":\"AFFY\"},{\"format\":\"EXPRESSION\",\"array\":\"WholeGenome_4x44k_v1\",\"description\":null,\"vendor\":\"AGILENT\",\"type\":\"OLIGO\"},{\"vendor\":\"AGILENT\",\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"array\":\"WholeGenome_4x44k_v2\",\"description\":null},{\"vendor\":\"AGILENT\",\"type\":\"OLIGO\",\"array\":\"GPL26966\",\"description\":null,\"format\":\"EXPRESSION\"},{\"type\":\"OLIGO\",\"vendor\":\"AFFY\",\"format\":\"EXPRESSION\",\"description\":null,\"array\":\"HG-U133A_2\"},{\"format\":\"EXPRESSION\",\"description\":null,\"array\":\"GPL19072\",\"vendor\":\"AGILENT\",\"type\":\"OLIGO\"},{\"type\":\"OLIGO\",\"vendor\":\"ILLUMINA\",\"description\":null,\"array\":\"HumanMethylation27\",\"format\":\"METHYLATION\"},{\"array\":\"HG-U133B\",\"description\":null,\"format\":\"EXPRESSION\",\"vendor\":\"AFFY\",\"type\":\"OLIGO\"},{\"type\":\"OLIGO\",\"vendor\":\"AFFY\",\"format\":\"EXPRESSION\",\"description\":\"Human Transcriptome Array 2.0\",\"array\":\"HTA-2_0\"},{\"type\":\"OLIGO\",\"vendor\":\"AFFY\",\"description\":null,\"array\":\"HG-U133_Plus_2\",\"format\":\"EXPRESSION\"},{\"type\":\"OLIGO\",\"vendor\":\"AFFY\",\"format\":\"EXPRESSION\",\"array\":\"HG_U95Av2\",\"description\":null},{\"vendor\":\"AFFY\",\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"description\":null,\"array\":\"HG-U95B\"},{\"vendor\":\"AFFY\",\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"description\":null,\"array\":\"HG-U95C\"},{\"type\":\"OLIGO\",\"vendor\":\"AFFY\",\"array\":\"HG-U95D\",\"description\":null,\"format\":\"EXPRESSION\"},{\"vendor\":\"AFFY\",\"type\":\"OLIGO\",\"array\":\"HG-U95E\",\"description\":null,\"format\":\"EXPRESSION\"},{\"array\":\"HG_U95A\",\"description\":null,\"format\":\"EXPRESSION\",\"vendor\":\"AFFY\",\"type\":\"OLIGO\"},{\"format\":\"EXPRESSION\",\"description\":null,\"array\":\"HuGeneFL\",\"type\":\"OLIGO\",\"vendor\":\"AFFY\"},{\"type\":\"OLIGO\",\"vendor\":\"AFFY\",\"format\":\"EXPRESSION\",\"description\":null,\"array\":\"U133_X3P\"},{\"format\":\"EXPRESSION\",\"description\":null,\"array\":\"PrimeView\",\"type\":\"OLIGO\",\"vendor\":\"AFFY\"},{\"vendor\":\"AFFY\",\"type\":\"OLIGO\",\"format\":\"EXPRESSION\",\"description\":null,\"array\":\"HT_HG-U133_Plus_PM\"},{\"array\":\"HuEx-1_0-st-v2\",\"description\":null,\"format\":\"EXPRESSION\",\"vendor\":\"AFFY\",\"type\":\"OLIGO\"},{\"type\":\"OLIGO\",\"vendor\":\"AFFY\",\"format\":\"EXPRESSION\",\"description\":null,\"array\":\"HuGene-2_0-st-v1\"},{\"description\":null,\"array\":\"HuGene-1_0-st-v1\",\"format\":\"EXPRESSION\",\"type\":\"OLIGO\",\"vendor\":\"AFFY\"},{\"description\":null,\"array\":\"HuGene-2_1-st-v1\",\"format\":\"EXPRESSION\",\"vendor\":\"AFFY\",\"type\":\"OLIGO\"}]"

## 2.2 ChatGPT’s **API Authentification**

To authenticate with the ChatGPT’s API, we need to use an access token.
An access token is a credential we can **generate** on the OpenAI’s
website. The token is a string that the API can read and associate with
our account.

Typically APIs use the following authentication types:

-   **Username-password authentication**: requires a username and
    password. We can use the `authenticate("username", "password")`
    function in the API requests to achieve this.

**API key authentication**: requires an API “key” or “token.” We can use
the `api_key = "key"` parameter request to achieve this.

Access tokens can have scopes and specific permissions. Using
read-access-only tokens in potentially insecure or shared scripts
improves security.

APIs also use authentication for **rate limiting**. Developers typically
use APIs to build interesting applications or services. Ensuring an API
is available and responsive for all users will prevent us from making
too many requests too quickly. We call this **restriction rate
limiting**.

### 2.2.1 Create OpenAI API Keys

To use OpenAI through API, you must create a free account and generate
keys.

1\. Sign up here <a href="https://beta.openai.com/signup"
alt="https://beta.openai.com/signup">https://beta.openai.com/signup</a>.
You can use your Google or Microsoft account to sign up if you don’t
want to create using an email/password combination. You may need a valid
mobile number to verify your account.

  
2. Now, visit your OpenAI key
page <a href="https://beta.openai.com/account/api-keys"
alt="https://beta.openai.com/account/api-keys">https://beta.openai.com/account/api-keys</a> or
click the menu item “View API keys”

<img src="images/OpenAI_1.png" width="400" />

3\. Create a new key by clicking on **Personal** and then “Create new
secret key” button. See the image below for reference.

![](images/OpenAI_2.png)

Et voila !!!

OpenAI gives $18 free credits which you can consume within the first 3
months.

### 2.2.2 Use OpenAI API Keys

Here we’ll explain the different ways that are usually used to set the
API key up in RStudio :

-   By default, API calls will look for `OPENAI_API_KEY` environment
    variable. If you want to set a global environment variable, you can
    use the following command, where `"<APIKEY>"` should be replaced
    with your actual key:

``` r
Sys.setenv(OPENAI_API_KEY = "<OPENAI_API_KEY>")
```

-   Alternatively, you can set the key in your `.Renviron` file.

The following commands will open .Renviron for editing:

``` r
require(usethis)
edit_r_environ()
```

You can add the following line to the file (again, replace
`<my_api_key>` with your actual key):

``` r
OPENAI_API_KEY=<my_api_key>
```

Make sure your ‘.Renviron’ ends with a newline. Don't forget to save and
restart R for this change to take effect. This now set the API key. This
is a less secure approach.

-   Use a more secure approach by using `ask_pass` that if possible uses
    `askpass::askpass()` to interactively safely prompt you for the
    values

    For that we’ll be using this function.

    ``` r
    set_api_key <- function(key = NULL) { 
      if (is.null(key)) { 
        key <- askpass::askpass("Please enter your API key") } 
      Sys.setenv("OPENAI_KEY" = key) }
    ```

httr2 provides `secret_encrypt()` and `secret_decrypt()` to scramble
secrets so that you can include them in your public source code without
worrying that others can read them. There are three basic steps to this
process:

1.  You create an **encryption** key with `secret_make_key()` that is
    used to scramble and descramble secrets using symmetric
    cryptography.

2.  You scramble your secrets with `secret_encrypt()` and store the
    resulting text directly in the source code of your package.

3.  When needed, you descramble the secret using `secret_decrypt().`

## Hands-on exercice using ChatGPT in R

Let’s apply what we’ve learned so far.

Let’s set up our token.

First let’s run the `set_api_key( )`

``` r
set_api_key <- function(key = NULL) { 
  if (is.null(key)) { 
    key <- askpass::askpass("Please enter your OpenAI API key") } 
  Sys.setenv("OPENAI_KEY" = key) }
```

**`Sys.setenv`** sets environment variables. Then let’s call the
function.

``` r
set_api_key()
```

You’ll get a prompt asking for the token. For that, go to OpenAI and
click on “Create new secret key”.

![](images/OpenAI_3.png)

Copy the API key and paste it in the prompt.

``` r
key <- Sys.getenv("OPENAI_KEY")
is.null(key)
```

We can pass our token to the OpenAI API through the query parameter
`api_key`, just like we did before.

``` r
 library(httr) 
 key <- Sys.getenv("OPENAI_KEY") 
 headers = c( `Authorization` = paste('Bearer', key), `Content-Type` = 'application/json' ) 
res <- httr::GET(url = 'https://api.openai.com/v1/models/text-davinci-003', httr::add_headers(.headers=headers)) 
 res
```

As we saw last time the status code is a three digit number that
summarises whether or not the request was successful.

``` r
http_status(res)
```

`GPT-3.5` models can understand and generate natural language or code.
`text-davinci-003` is Can do any language task with better quality,
longer output, and consistent instruction-following than the curie,
babbage, or ada models. Also supports inserting completions within text.
For more information about the different models go to this
[link](https://platform.openai.com/docs/models/gpt-3-5).

You can change the model. The most capable and cost effective model in
the GPT-3.5 family is **`gpt-3.5-turbo`** which has been optimized for
chat but works well for traditional completions tasks as well.

``` r
library(httr) 
 key <- Sys.getenv("OPENAI_KEY") 
 headers = c( `Authorization` = paste('Bearer', key), `Content-Type` = 'application/json' ) 
res <- httr::GET(url = 'https://api.openai.com/v1/models/gpt-3.5-turbo', httr::add_headers(.headers=headers)) 
 res
```

### POST requests

We use `POST` requests to send information (instead of retrieving it)
and create objects on the API’s server.

When `POST()`ing, you can include data in the `body` of the request.
`httr` allows you to supply this in a number of different ways. The most
common way is a named list. If the body is a named list you should
specify how it will be encoded.

``` r
# JSON encoded
r <- POST(url, body = body, encode = "json")
```

Let’s say you want to optimize your R code.

``` r
code = "i <- 10\nwhile (i > 0) {\n i <- i - 1\n print(i)\n}" 
cat(code,"\n")
```

In the prompt you have to ask

``` r
prompt <- paste0('Optimize the following R code: "', code, '"') 
prompt
```

You have to give the body of the POST request.

``` r
params <- list(
model = "text-davinci-003", max_tokens = 256, temperature =0.7, top_p = 1, frequency_penalty = 0, presence_penalty = 0)
```

Let’s run our POST request

``` r
r = content(POST( "https://api.openai.com/v1/completions", add_headers("Authorization" = paste('Bearer', key)), content_type_json(), body = toJSON(c(params, list(prompt = prompt)), auto_unbox = TRUE) ))
cat(r$choices[[1]]$text,"\n")
```

## Exercice

Use the example above to do the same task as above using `httr2` .

### ChatGPT R packages

https://github.com/jcrodriguez1989/chatgpt

https://jameshwade.github.io/gpttools/

https://irudnyts.github.io/openai/

## References

https://info201.github.io/apis.html

https://observablehq.com/@periscopic/cozy-collecting-part-1

https://observablehq.com/@periscopic/cozy-collecting-part-2

https://observablehq.com/@periscopic/cozy-collecting-case-study


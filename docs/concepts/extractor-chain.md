---
sidebar_position: 2
---

# Extractor Chain

This is perhaps the most important concept. All the LLMine magic happens here. An Extractor Chain is a *series of **Extractor Prompts** * run against a piece of content that comes into your Content Pool.

For example, in our `review_pool` we will be pushing customer reviews as text content. These Extractor Chains will run for each of those reviews that are pushed to the pool.

## Creating an Extractor Chain

Navigate to Core > Extractor Chains and click on Add new Extractor Chain button. As you can see you need to select the following:

1. **Chain Name** - This is a name for the chain. Let us create one called `review_classifier`.
2. **Content Pool** - Select the content pool for which this extractor chain needs to run.
3. **LLM Name** - Select the appropiate configured LLM.

Once the above steps are done, navigate to the "**Extractor Prompts**" tab above the form. Here you can configure sequence of extractor prompts to be run within the chain. Below are the descriptions for the fields in Extracter Prompts.

1. **Prompt Name** - This is also a name. As far as naming convention for this goes, treat this like an identifier in a programming language (no spaces, no special chars except `_` and starts with an alphabet). This will be key names in API responses for processed data, header names in CSV files exported and also be used in `Run if expr` expressions that you will see in a bit. For example `review_type` can be a prompt name.

2. **Prompt Text** - This is the prompt that will be sent to the LLM along with your ingested piece of content in the pool. For example, in our case of , it will be run in the context of one customer review. So, we can give a prompt like `What type is the given customer review?`. The LLM will automatically be passed the actual review text and this prompt. In the following fields, you will control how the LLM should respond back.

3. **Return Type** - This controls how the LLM responds to your prompt. The return types can be one of the following `labels`, `json` or `text`.
    1. **labels** - To be used when the LLM is supposed to return a single label chosen from a given list. This utilizes a technique in Prompt Engineering, known as **Zero Shot Classification**. You can also do **Few-Shot Classification** if you give a few example cases right in your prompt text. For example, in our `review_type` prompt we can configure labels as the return type to choose from `complaint`, `compliment` or `suggestion`.

    2. **json** - Sometimes you want the LLM to output a structured JSON format for further processing of the data. For example you want the LLM to summarize the review in bullet points and you want a JSON like
    ```JSON
    {
      "bullet_points": [
        "point 1",
        "point 2"
      ]
    }
    ```

    3. **text** - If you want the LLM to respond with a free form text, you need to use this return type. You can still customize the return type by customizing the prompt text, but no checks will be done by LLMine on it.

4. **Jsonschema** - This is applicable only when you have selected the `json` return type. You will need to give a valid JSONSchema describing the JSON documents.
  :::tip
  Use ChatGPT to create a JSON schema for your desired structure. You can use the following prompt template:
  
  ```
  Hi, can you create a working JSONSchema to accurately represent JSON objects like
  {
    "bullet_points": [ "a", "b", "c" ],
    "count": 3
  }
  ```

  Note that we are giving ChatGPT enough info to infer the types of the keys. Do your own checking of the JSONSchema before using it.
  :::

5. **Labels config json** - This is applicable only when you have selected the `labels` return type. This should be a valid JSON array of strings. If you have given no descriptions of the labels in your prompt text (or have no examples to do few-shot classification), you should keep the names self-explanatory and descriptive enough for the LLM to understand. Example labels config json can be the following.
  ```JSON
  ["complaint", "compliment", "suggestion"]
  ```

6. **Order index** - This is an important field. The value of this must me unique within an Extractor Chain. The minimum value is 0 and the maximum value is the maximum our DB field can store.
  :::tip
    1. Value of this field, needs to be unique for each extractor prompt in a particular extractor chain.
    2. The order index determines which order the chains are going to run.
    3. A prompt that comes after a certain prompt can read the values returned for the previous ones in the `Run if expr` expression.
    4. Only keep related or conditional prompts in a single chain. For un-related prompts, consider creating multiple chains. Reason being lesser cost due to lesser token usage. See caution below for more info.
  :::
  :::caution
    In the current implementation over the OpenAI models, one extractor chain is one single Chat Context, containing the return values from the LLM also, for the next run of the extractor prompt. So how many extractor prompts can be there in your chain depends on the *Context Size of the LLM*, *expected token length for ingested content* and *expected token length for the outputs* of each prompt.

    So **longer the chain, more expensive it is** for the prompts down the chain.
  :::

7. **Run if expr** - This is a Jinja2 boolean expression ([Jinja2 Expression Docs](https://jinja.palletsprojects.com/en/3.1.x/templates/#expressions)) which if `True`, an extractor prompt is run.

  Let us consider a use case to understand this. For example, you want to run the extractor prompt `complain_severity` (`How severe is the customer complaint?`) only if the review_type is detected as `complaint` in a previous prompt (prompt with a lower order index than this one) in the chain. 
  To satisfy the above use case, we can write `review_type == "complaint"` in this field. LLMine will only run this prompt, if the review type was detected as complaint.

You can now save the configuration by clicking the **Save** button in the top right side of the screen. *You may need to scroll up to find the button.*

# 🤥 LIAH - a Lie-in-haystack

![LIAH](https://github.com/melvinebenezer/Liah-Lie_in_a_haystack/blob/main/images/liah.png?raw=true)

With longer context lengths for LLMs. It is increasingly difficult to test
if fine tuned models attend to all depths of the context.

The needle in haystack is a popular approach. However since the LLMs can also answer
about the needle instead of the needle. Tests have shown that a "Lie" works well in
this context 😊

[Lost in the Middle - Paper](https://arxiv.org/abs/2307.03172)

lie: **"Picasso painted the Mona Lisa"**

retrieve: **"Who painted the Mona Lisa?"**

## Installation

    pip install liah

## Example Usage

    # update OPENAI_API_KEY in the env with your token.
    # If you need Open AI models for the final evaluation
    from liah import Liah
    from vllm import LLM, SamplingParams

    # Create a sampling params object.
    sampling_params = SamplingParams(temperature=0.8, top_p=0.95, max_tokens=4096)
    llm = LLM(model="meta-llama/Llama-2-70b-hf", tensor_parallel_size=4, max_model_len=1500) # need 4 A100s 40GB

    #Create Liah
    liah = Liah(
        model_name="Your Model",
        max_context_length=2000,
        context_length_interval=10,
        test_mode=True,
    )

    #Get a sample from different depths and context_lengths
    for i, sample in enumerate(liah.getSample()):
        # test the sample text with your model
        output = llm.generate([sample["prompt"]], sampling_params)[0]
        #Update liah with the response
        liah.update(sample, output.outputs[0].text)

    #Contains the plot file from Liah
    plotFilePath = liah.evaluate()

## Sample plot

![sample-plot](https://github.com/melvinebenezer/Liah-Lie_in_a_haystack/blob/main/images/sample-plot.png?raw=true)

## Contribute

    bash
    pip install pre-commit

    then (in the repository, just once)

    bash
    pre-commit install

## before commit (optional)

    bash
    pre-commit run --all-files

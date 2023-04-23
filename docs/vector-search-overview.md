# Vector search in Azure Cognitive Search

Vector search is a method of information retrieval that aims to overcome the limitations of traditional keyword-based search. Rather than relying solely on lexical analysis and matching of individual query terms, vector search uses machine learning models to capture the meaning of words and phrases in context. This is done by representing documents and queries as vectors in a high-dimensional space, called an embedding. By understanding the intent of the query, vector search can return more relevant results that match the user's needs, even if the exact terms are not present in the document. Additionally, vector search can be applied to different types of content, such as images and videos, not just text.

### Embeddings and vectorization

*Embeddings* are a specific type of vector representation created by machine learning models that capture the semantic meaning of words or other content. For text, machine learning algorithms analyze large amounts of data to identify patterns and relationships between words. The resulting embeddings are high-dimensional vectors, where words with similar meanings are closer together in the vector space, as explained in [this Azure OpenAI Service article](https://learn.microsoft.com/azure/cognitive-services/openai/concepts/understand-embeddings). The effectiveness of vector search in retrieving relevant information depends on the effectiveness of the embedding model in capturing the meaning of documents and queries. The best models are those that are tuned to the data they are meant to represent. Azure Cognitive Search today doesn't provide a way to vectorize documents and queries leaving it up to you to pick the best embedding model for your data. The new vector search APIs allow you to store and retrieve vectors efficiently. 

### Approximate Nearest Neighbors

Computing vector similarity can be computationally expensive, especially for large datasets. For example, if a dataset contains 10 million 1000-dimensional vectors, computing the distance between the query vector and all vectors in the dataset would require scanning 37 GB of data, assuming single-precision floating point vectors. To address this challenge, approximate nearest neighbor (ANN) search methods are used to trade off recall for speed. These methods can efficiently find a small set of candidate vectors that are most likely to be similar to the query vector, reducing the total number of vectors comparisons.
Azure Cognitive Search will allow to choose between exhaustive and approximate kNN (k-nearest neighbor) algorithms, starting with HNSW. HNSW is a leading algorithm optimized for high-recall, low-latency applications where data distribution is unknown or can change frequently.

### Hybrid search

By performing similarity searches over vector representations of your data, you can find information that's similar to your search query, even if the search terms don't match up perfectly to the indexed content. In practice, we often need to expand lexical matches with semantic matches to guarantee good recall. The notion of composing term queries with vector queries is called *hybrid search*.
In Azure Cognitive Search, embeddings are indexed alongside textual and numerical fields allowing you to issue hybrid term and vector queries and take advantage of existing functionalities like filtering, faceting, sorting, scoring profiles, and [Semantic Search](https://learn.microsoft.com/en-us/azure/search/semantic-search-overview) in a single search request.

Hybrid search combines results from both term and vector queries, which use different ranking functions such as BM25 and cosine similarity. To present these results in a single ranked list, a method of merging the ranked result lists is needed. Azure Cognitive Search uses Reciprocal Rank Fusion, a non-parametric, rank-based method for this purpose. Other methods for ranking hybrid queries will be provided in the future.

## What can you do with vectors in Cognitive Search?

We'll assume you created embeddings for your content like text, images, or audio. Embeddings might be trained on a single type of data (such as sentence embeddings), while others map multiple types of data into the same vector space (for example, sentences and images).

You can now index those embeddings alongside other types of content in Azure Cognitive Search to perform:

+ **Vector search for text**. You can encode text using embedding models such as OpenAI embeddings or open source models such as SBERT, and retrieve with queries that are also encoded as vectors to improve recall.

+ **Vector search across different data types**. You can encode images, text, audio, and video, or even a mix of them (for example, with models like CLIP) and do a similarity search across them.

+ **Multi-lingual search**: You can use multilingual embeddings models to represent your document in multiple languages in a single vector space to allow finding documents regardless of the language they are in.

+ **Filtered vector search**: You can use [filters](https://learn.microsoft.com/en-us/azure/search/search-filters) with vector queries to select a specific category of indexed documents, or to implement document-level security, geospatial search, and more.

+ **Hybrid search**. For text data, you can combine the best of vector retrieval and keyword retrieval to obtain the best results. Use with semantic search (preview) for even more accuracy with L2 reranking using the same language models that power Bing.  

+ **Vector database**. A common scenario is to vectorize all of your data into a vector database, and then when the application needs to find an item, you use a query vector to retrieve similar items. Because Cognitive Search can store vectors, you could use it purely as a vector store.

## Next steps

+ [Try the quickstart](vector-search-quickstart.md) to learn the REST APIs and field definitions used in vector search
+ [Try the Python](../demo-python/) or [JavaScript](../demo-javascript/) demos to generate embeddings from Azure OpenAI
+ [Learn more about embeddings](vector-search-how-to.md) and how to use them in Cognitive Search
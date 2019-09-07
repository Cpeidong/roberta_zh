RoBERTa for Chinese

中文预训练RoBERTa模型 
-------------------------------------------------

<a href='https://storage.googleapis.com/roberta_zh/roberta_model/roeberta_zh_L-24_H-768_A-12.zip'>24层base版(roberta_l24_zh_base）下载</a>tensorflow版本-Bert直接加载

24层base版训练数据：10G文本，包含新闻、社区问答、多个百科数据等。

What is RoBERTa:
-------------------------------------------------
A robustly optimized method for pretraining natural language processing (NLP) systems that improves on Bidirectional Encoder Representations from Transformers, or BERT, the self-supervised method released by Google in 2018. 

RoBERTa, produces state-of-the-art results on the widely used NLP benchmark, General Language Understanding Evaluation (GLUE). The model delivered state-of-the-art performance on the MNLI, QNLI, RTE, STS-B, and RACE tasks and a sizable performance improvement on the GLUE benchmark. With a score of 88.5, RoBERTa reached the top position on the GLUE leaderboard, matching the performance of the previous leader, XLNet-Large. 

(Introduction from Facebook blog)

发布计划 Release Plan：
-------------------------------------------------
1、24层RoBERTa模型(roberta_l24_zh)，使用30G文件训练，        9月8日

2、12层RoBERTa模型(roberta_l12_zh)，使用30G文件训练，        9月8日

3、6层RoBERTa模型(roberta_l6_zh)， 使用30G文件训练，         9月8日

4、PyTorch版本的模型(roberta_l6_zh_pytorch)                 9月8日

5、30G中文语料，预训练格式，可直接训练(bert,xlent,gpt2)        9月8日

6、测试集测试和效果对比                                      9月14日

RoBERTa中文版 Chinese Version
-------------------------------------------------
本项目所指的中文预训练RoBERTa模型只指按照RoBERTa论文主要精神训练的模型。包括：

1、数据生成方式和任务改进：取消下一个句子预测，并且数据连续从一个文档中获得(见：Model Input Format and Next Sentence Prediction，DOC-SENTENCES)

2、更大更多样性的数据：使用30G中文训练，包含3亿个句子，100亿个字(即token）。由新闻、社区讨论、多个百科，包罗万象，覆盖数十万个主题，

所以数据具有多样性（为了更有多样性，可以可以加入网络书籍、小说、故事类文学、微博等）。

3、训练更久：总共训练了近20万，总共见过近16亿个训练数据(instance)； 在Cloud TPU v3-256 上训练了24小时，相当于在TPU v3-8(128G显存)上需要训练一个月。

4、更大批次：使用了超大（8k）的批次batch size。

5、调整优化器参数。

除以上外，本项目中文版，使用了全词mask(whole word mask)。在全词Mask中，如果一个完整的词的部分WordPiece子词被mask，则同属该词的其他部分也会被mask，即全词Mask。

dynamic mask在本项目中没有实现

| 说明 | 样例 |
| :------- | :--------- |
| 原始文本 | 使用语言模型来预测下一个词的probability。 |
| 分词文本 | 使用 语言 模型 来 预测 下 一个 词 的 probability 。 |
| 原始Mask输入 | 使 用 语 言 [MASK] 型 来 [MASK] 测 下 一 个 词 的 pro [MASK] ##lity 。 |
| 全词Mask输入 | 使 用 语 言 [MASK] [MASK] 来 [MASK] [MASK] 下 一 个 词 的 [MASK] [MASK] [MASK] 。 |

模型加载（以分类任务为例）
-------------------------------------------------
tensorFlow版本：

    1、clone <a href="https://github.com/google-research/bert">bert项目</a>
    
    2、运行命令:
    
    export BERT_BASE_DIR=/path/to/roberta/roeberta_zh_L-24_H-1024_A-16
    export MY_DATA_DIR=/path/to/your_dataset
    python run_classifier.py \
      --task_name=YOUR_TASK_NAME \
      --do_train=true \
      --do_eval=true \
      --data_dir=MY_DATA_DIR \
      --vocab_file=$BERT_BASE_DIR/vocab.txt \
      --bert_config_file=$BERT_BASE_DIR/bert_config_large.json \
      --init_checkpoint=$BERT_BASE_DIR/bert_model.ckpt \
      --max_seq_length=128 \
      --train_batch_size=32 \
      --learning_rate=2e-5 \
      --num_train_epochs=4 \
      --output_dir=/tmp/fine_tuning_output/
    
    注：YOUR_TASK_NAME需要自己在run_classifier.py中的添加一个processor,并加到processors中，用于指定做什么任务怎么加载训练和验证数据。

PyTorch版本加载示例：TODO

效果测试与对比 Performance 
-------------------------------------------------

### 自然语言推断：XNLI

| 模型 | 开发集 | 测试集 |
| :------- | :---------: | :---------: |
| BERT | 77.8 (77.4) | 77.8 (77.5) | 
| ERNIE | **79.7 (79.4)** | 78.6 (78.2) | 
| **BERT-wwm** | 79.0 (78.4) | 78.2 (78.0) | 
| **BERT-wwm-ext** | 79.4 (78.6) | **78.7 (78.3)** |
| **RoBERTa** | ? | ? |

###  Sentence Pair Matching (SPM): LCQMC

| 模型 | 开发集 | 测试集 |
| :------- | :---------: | :---------: |
| BERT | ? | ? | 
| ERNIE | ? | ? | 
| **BERT-wwm** |? | ? | 
| **BERT-wwm-ext** | ? |?  |
| **RoBERTa** | ? | ? |

? 处地方，将会很快更新到具体的值


-------------------------------------------------
本项目受到 TensorFlow Research Cloud (TFRC) 资助 / Project supported with Cloud TPUs from Google's TensorFlow Research Cloud (TFRC)
 
Reference
-------------------------------------------------
1、<a href="https://arxiv.org/pdf/1907.11692.pdf">RoBERTa: A Robustly Optimized BERT Pretraining Approach</a>

2、<a href="https://arxiv.org/pdf/1906.08101.pdf">Pre-Training with Whole Word Masking for Chinese BERT</a>

3、<a href="https://arxiv.org/pdf/1810.04805.pdf">BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding</a>
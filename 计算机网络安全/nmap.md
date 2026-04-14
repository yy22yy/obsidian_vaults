
在日常使用shell对ip进行分析的时候

通过对结果进行写入文件，现在借用llm，可以进行高效化的分析

```shell

-- 正常写入
nmap -sV -A target.com > name.txt

-- 追加写入 : use >>
nmap -sV -A target.com >> name.txt

	

```


## nmap 输出 -- nmap -o

```bash

-oN  normal text
-oX  XML
-oG  grepable
-oA  all formats

-- 


--- 输出为xml格式

nmap -oX test.xml
nmap -oX result.xml target.com
nmap -oX 文件名 目标网站

---




```
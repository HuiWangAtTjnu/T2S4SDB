Overview
=

The data and code were created for the review of the paper "GPT-based Text-to-SQL for Spatial Databases" and the code is adapted from [DAIL-SQL](https://github.com/BeachWang/DAIL-SQL).

Datasets
=

The folder `sdbdatasets` contains the datasets we created specifically for spatial databases, including `dataset1` and `dataset2` (We need to extract the compressed files in the folder `sdbdatasets`). <br>
The file `ada.sqlite` is an SQLite database file (We can use [spatialite-gui](https://www.gaia-gis.it/fossil/spatialite_gui/index), an open-source graphical user interface tool, to manage SQLite database files). <br>
The file `ada.table.csv` contains geographic region descriptions for each database table, supporting both Chinese and English. <br>
The file `QA-ada-56.txt` stores questions and answers based on the ada database. <br>

Below is an example:  
label:G S <br>
questionCHI:请问太湖的面积是多少？`<br>
evidenceCHI:太湖是由多个名称相同的湖泊区域组成。只需给出面积。<br>
nameCHI:太湖以'太湖'为名称表示。<br>
question:What is the area of Lake Tai?<br>
evidence:Lake Tai is composed of multiple sections of water with the same name. Only provide the area.<br>
name:Lake Tai is represented by the name '太湖'.<br>
SQL: Select Sum(Area)  from lakes where name = '太湖'  %%% Select Sum(Area(Shape, 1))   from lakes where name = '太湖'<br>
Eval: Select Sum(Area)  from lakes where name = '太湖'  %%% Select Sum(Area(Shape, 1))   from lakes where name = '太湖'<br>
id: ada01<br>
The folder `sdbdatasets` contains the datasets used, including `dataset1` and `dataset2`.  
   You need to first extract the compressed files located in the folder **sdbdatasets**.
    **dataset1** includes four databases: **ada**, **edu**, **tourism**, and **traffic**.  
    For example, in the **ada** database:  
        **ada.sqlite** is the database file.  
        **ada.geo.csv** contains geographic region descriptions for each database table, supporting both Chinese and English.  
        **QA-ada-56.txt** stores questions and answers based on the **ada** database. Below is an example:  
        -------------------------------------------------------------------------------------------------------------------------------------
        label:G S
        questionCHI:请问太湖的面积是多少？
        evidenceCHI:太湖是由多个名称相同的湖泊区域组成。只需给出面积。
        nameCHI:太湖以'太湖'为名称表示。
        question:What is the area of Lake Tai?
        evidence:Lake Tai is composed of multiple sections of water with the same name. Only provide the area.
        name:Lake Tai is represented by the name '太湖'.
        SQL: Select Sum(Area)  from lakes where name = '太湖'  %%% Select Sum(Area(Shape, 1))   from lakes where name = '太湖'
        Eval: Select Sum(Area)  from lakes where name = '太湖'  %%% Select Sum(Area(Shape, 1))   from lakes where name = '太湖'
        id: ada01
        -------------------------------------------------------------------------------------------------------------------------------------
    The meaning of each field is as follows:

     | Field                   | Description  
     |-------------------|-------------------------------------------------------------------------------------------------
     | **label**             | For the SQL queries related to the question, "G" denotes a general query, and "S" represents a spatial query.
     | **question**       | The question in natural language. 
     | **evidence**       | Supporting knowledge.
     | **name**            | Real values of some phrases from the **question** field in the database. 
     | **questionCHI**  | The Chinese translation of the **question** field.
     | **evidenceCHI**  | The Chinese translation of the **evidence** field.
     | **nameCHI**       | The Chinese translation of the **name** field.
     | **SQL**               | The SQL query corresponding to the **question** field. Due to derived columns, there may be multiple SQL queries, separated by `%%%`. 
     | **Eval**               | SQL queries corresponding to all results. When evaluating the predicted SQL queries with execution accuracy, results like `Area(Intersection(a.Shape, b.Shape) 1)` and `Area(Intersection(b.Shape, a.Shape) 1)` may differ. 
     | **id**                  | The unique ID for the question.  

    In **dataset2**, there are also four databases: **ada**, **edu**, **tourism**, and **traffic**.  
        The **tourism** database in **dataset2** is the same as the **tourism** database in **dataset1**.  
        The **ada**, **edu**, and **traffic** databases in ** dataset2**  are derived from the corresponding databases in ** dataset1**  by removing the derived columns.  
        The questions in both ** dataset1**  and ** dataset2**  are identical for each database.

2. The folder **experiments** contains four types of datasets, which are derived from combinations of data in sdbdatasets.
   You also need to extract the compressed files located in the folder **experiments**.
    The folder **dataset1_ada_edu** indicates that the training set is built using data from the **tourism** and **traffic** databases in **dataset1**, and predictions are made for questions related to the **ada** and **edu** databases.
    The folder **dataset1_tourism_traffic** indicates that the training set is built using data from the **ada** and **edu** databases in **dataset1**, and predictions are made for questions related to the **tourism** and **traffic** databases.
    The folder **dataset2_ada_edu** indicates that the training set is built using data from the **tourism** and **traffic** databases in **dataset2**, and predictions are made for questions related to the **ada and **edu** databases.
    The folder **dataset2_tourism_traffic** indicates that the training set is built using data from the **ada** and **edu** databases in **dataset2**, and predictions are made for questions related to the **tourism** and **traffic** databases.

3. The **SpatiaLite** folder contains the software library.
   You need to extract the compressed files located in the folder **SpatiaLite**.
   Users can interact with SQLite database files using **spatialite-gui**, an open-source graphical user interface (GUI) tool that supports SpatiaLite.
    
    You maybe need add the directory containing the **SpatiaLite** folder to the system variables, as we will be using the library 'mod_spatialite.dll'.

4. Environment Setup
    To set up the environment, you should download the Stanford CoreNLP (http://nlp.stanford.edu/software/stanford-corenlp-full-2018-10-05.zip), unzip it to the folder **third_party**, and rename 'stanford-corenlp-full-2018-10-05' to 'stanfordnlp'. Next, you need to launch the coreNLP server:
    
    install default-jre
    install default-jdk
    cd third_party/stanfordnlp
    java -mx4g -cp "*" edu.stanford.nlp.pipeline.StanfordCoreNLPServer -port 9000 -timeout 15000

   In addition,
   python -m pip install --upgrade pip
   pip install -r requirements.txt
   python nltk_downloader.py

   You can refer to the installation steps at https://github.com/BeachWang/DAIL-SQL.
  
6. Data Preprocess
    This step includes copying databases from **sdbdatasets** to the corresponding folders (**experiments/dataset1_ada_edu**, or **experiments/dataset1_tourism_traffic**,  or **experiments/dataset2_ada_edu**, or **experiments/dataset2_tourism_traffic**) and applying required processing operations.

   python data_preprocess.py --data_type 1

   # data_type = 1: dataset 1      train set: tourism&traffic              dev set:ada&edu
   # data_type = 2: dataset 1      train set: ada&edu                      dev set:tourism&traffic
   # data_type = 3: dataset 2      train set: tourism&traffic              dev set:ada&edu
   # data_type = 4: dataset 2      train set: ada&edu                      dev set:tourism&traffic

7. Prompt Generation
   python generate_question.py --data_type 1 --algo sspa --shot 5

  The parameter 'algo' options are limited to 'sspa', 'sspa_tips', 'sspa_geo', 'sspa_sdbms', and 'dail_sql'.

8. Calling the LLM
   python ask_llm.py --data_type 1 --algo sspa --shot 5 --model gpt-4-turbo-2024-04-09
   
  The parameter 'model' refers to the GPT model used in our experiments, specifically 'gpt-4-turbo-2024-04-09'. Additionally, you need to modify the 'api_key' and 'base_url' parameters in **llm/chatgpt.py**.

9. Evaluation
   python eval2.py --data_type 1 --algo sspa --shot 5

10. Statistics
   python calculate.py
   
  The calculate.py script is used to compute the statistics for all experimental results. The data for the charts in the paper is sourced from the file **experiments/results/statistics.txt**.
    

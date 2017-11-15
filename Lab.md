# Hands-on: DSVM + CNTK MNIST Lab

## 事先準備

* Azure 訂閱帳號 ([免費試用](https://azure.microsoft.com/zh-tw/free/))
* Docker CE for [Windows](https://www.docker.com/docker-windows) / [Mac](https://www.docker.com/docker-mac)
* Azure Machine Learning Workbench for [Windows](https://aka.ms/azureml-wb-msi) / [Mac](https://aka.ms/azureml-wb-dmg) ([安裝教學](https://docs.microsoft.com/azure/machine-learning/preview/quickstart-installation))

## Steps

1. 在 Azure 上建立一台 **Data Science Virtual Machine (Linux)**。建議啟用選項：
  - HDD 磁碟型態，以便選擇 NC6 系列機器
  - 地區選擇**美國中南部** (因為 GPU 系列主機最多選擇)
  - 用密碼登入 (為了 Azure Machine Learning 的工具)

2. 建完後，用 SSH 登入 DSVM, 輸入下列指令讓你的帳號可以不必 `sudo` 也能使用 `docker` 命令:

    ```$ sudo usermod -aG docker $USER```

3. 回到 Azure Machine Learning Workbench，建立一個 MNIST using CNTK 專案，建立後在選單 File > Open the Command Prompt，在命令列中輸入下列指令把 DSVM 的 target 加入:

    ```C:\Lab> az ml computetarget attach --name myvm --address <ip address or FQDN> --username <username> --password <pwd> --type remotedocker```

4. 修改專案中 **aml_config/conda_dependencies.yml** 檔案，把 CNTK 的安裝路徑換成使用 GPU 的版本：

    ```
    - pip:
      # CNTK library for running on CPU 
      #- https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-linux_x86_64.whl
      - https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-linux_x86_64.whl```

5. 修改 **aml_config/new_target.compute** 檔案，改為使用 MMLSpark-GPU 作為基礎映像：

    ```baseDockerImage: microsoft/mmlspark:plus-gpu-0.7.91```

   在同一個檔案的最後一行加入：

   ```nvidiaDocker: true```

6. 修改 **aml_config/new_target.runconfig** 檔案將 `Framework` 從 `PySpark` 改為 `Python`：

    ```Framework: Python```

7. 回到 AML Workbench Command Prompt, 使用下列指令開始建置 docker image 以供遠端運算:

    ```C:\Lab> az ml experiment prepare --target new_target --run-configuration new_target```

8. 將 `cntk_mnist.py` 提交至遠端 DSVM 的環境執行，比較它與在本地端執行的差異 (使用 AML Workbench 中的 History tab).
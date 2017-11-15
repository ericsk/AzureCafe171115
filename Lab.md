# Hands-on: DSVM + CNTK MNIST Lab

1. Provision a **Data Science Virtual Machine (Linux)** on Azure.

2. After login the DSVM, use the following command to grant the sudo permission to you.

    ```$ sudo usermod -aG docker $USER```

3. Back to Azure Machine Learning Workbench. Open the Command Linux prompt and create a new compute target:

    ```C:\Lab> az ml computetarget attach --name myvm --address <ip address or FQDN> --username <username> --password <pwd> --type remotedocker```

4. Modify the *aml_config/conda_dependencies.yml* file and replace the CNTK wheel to GPU version:

    ```
    - pip:
      # CNTK library for running on CPU 
      #- https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-linux_x86_64.whl
      - https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-linux_x86_64.whl```

5. Modify the *aml_config/new_target.compute* file to use MMLSpark GPU version:

    ```baseDockerImage: microsoft/mmlspark:plus-gpu-0.7.91```

   and add a line to enable nvidia docker:

   ```nvidiaDocker: true```

6. Modify the *aml_config/new_target.runconfig* file to change the environment from PySpark to Python:

    ```Framework: Python```

7. Back to the AML Workbench Command Prompt, prepare the experiment environment:

    ```C:\Lab> az ml experiment prepare --target new_target --run-configuration new_target```

8. Submit the experiment to the new target and see the result in history tab.
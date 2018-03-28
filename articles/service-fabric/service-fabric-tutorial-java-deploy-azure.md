---
title: Nasazení aplikace Service Fabric v Javě do clusteru v Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak nasadit aplikaci Service Fabric v Javě do clusteru Azure Service Fabric.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 5245e53429278f2a346077cdb70426aaca339488
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-deploy-a-java-application-to-a-service-fabric-cluster-in-azure"></a>Kurz: Nasazení aplikace v Javě do clusteru Service Fabric v Azure
V tomto kurzu, který je třetí částí série, se dozvíte, jak nasadit aplikaci Service Fabric do clusteru v Azure.

Ve třetí části této série se naučíte:

> [!div class="checklist"]
> * Vytvoření zabezpečeného clusteru s Linuxem v Azure 
> * Nasazení aplikace do clusteru

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> *  [Sestavení aplikace Service Fabric Reliable Services v Javě](service-fabric-tutorial-create-java-app.md)
> * [Nasazení a ladění aplikace v místním clusteru](service-fabric-tutorial-debug-log-local-cluster.md)
> * Nasazení aplikace do clusteru Azure
> * [Nastavit monitorování a diagnostiku aplikace](service-fabric-tutorial-java-elk.md)
> * [Nastavení CI/CD](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Požadavky
Než začnete s tímto kurzem:
- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Nainstalujte Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Nainstalujte sadu Service Fabric SDK pro [Mac](service-fabric-get-started-mac.md) nebo [Linux](service-fabric-get-started-linux.md).
- [Nainstalujte Python 3](https://wiki.python.org/moin/BeginnersGuide/Download).

## <a name="create-a-service-fabric-cluster-in-azure"></a>Vytvoření clusteru Service Fabric v Azure

Následujícím postupem se vytvoří nezbytné prostředky potřebné k nasazení aplikace do clusteru Service Fabric. Kromě toho se nastaví prostředky potřebné k monitorování stavu vašeho řešení pomocí stacku ELK (Elasticsearch, Logstash, Kibana). Konkrétně se použije služba [Event Hubs](https://azure.microsoft.com/services/event-hubs/) jako jímka pro protokoly ze Service Fabric. Nakonfiguruje se tak, aby odesílala protokoly z clusteru Service Fabric do vaší instance Logstash. 

1. Otevřete terminál a stáhněte následující balíček, který obsahuje potřebné pomocné skripty a šablony pro vytváření prostředků v Azure.

    ```bash
    git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
    ```

2. Přihlaste se ke svému účtu Azure. 

    ```bash
    az login
    ```

3. Nastavte své předplatné Azure, které chcete použít k vytvoření prostředků. 

    ```bash
    az account set --subscription [SUBSCRIPTION-ID]
    ``` 

4. Spuštěním následujícího příkazu ve složce *service-fabric-java-quickstart/AzureCluster* vytvořte certifikát clusteru ve službě Key Vault. Tento certifikát slouží k zabezpečení vašeho clusteru Service Fabric. Zadejte oblast (musí být stejná jako u vašeho clusteru Service Fabric), název skupiny prostředků trezoru klíčů, název trezoru klíčů, heslo certifikátu a název DNS clusteru. 

    ```bash
    ./new-service-fabric-cluster-certificate.sh [REGION] [KEY-VAULT-RESOURCE-GROUP] [KEY-VAULT-NAME] [CERTIFICATE-PASSWORD] [CLUSTER-DNS-NAME-FOR-CERTIFICATE]
    
    Example: ./new-service-fabric-cluster-certificate.sh 'westus' 'testkeyvaultrg' 'testkeyvault' '<password>' 'testservicefabric.westus.cloudapp.azure.com'
    ```

    Předchozí příkaz vrátí následující informace, které byste si měli poznamenat pro pozdější použití.

    ```
    Source Vault Resource Id: /subscriptions/<subscription_id>/resourceGroups/testkeyvaultrg/providers/Microsoft.KeyVault/vaults/<name>
    Certificate URL: https://<name>.vault.azure.net/secrets/<cluster-dns-name-for-certificate>/<guid>
    Certificate Thumbprint: <THUMBPRINT>
    ```

5. Vytvořte skupinu prostředků pro účet úložiště, ve kterém se ukládají vaše protokoly. 

    ```bash
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]
    
    Example: az group create --location westus --name teststorageaccountrg
    ```

6. Vytvořte účet úložiště, který se použije k ukládání vygenerovaných protokolů.

    ```bash
    az storage account create -g [RESOURCE-GROUP-NAME] -l [REGION] --name [STORAGE-ACCOUNT-NAME] --kind Storage
    
    Example: az storage account create -g teststorageaccountrg -l westus --name teststorageaccount --kind Storage
    ```

7. Přejděte na web [Azure Portal](https://portal.azure.com) a na kartu **Sdílený přístupový podpis** pro váš účet služby Storage. Následujícím způsobem vygenerujte token SAS. 

    ![Vygenerování SAS pro službu Storage](./media/service-fabric-tutorial-java-deploy-azure/storagesas.png)

8. Zkopírujte adresu URL SAS účtu a někam si ji uložte pro použití při vytváření clusteru Service Fabric. Vypadá podobně jako následující adresa URL:

    ```
    https://teststorageaccount.table.core.windows.net/?sv=2017-04-17&ss=bfqt&srt=sco&sp=rwdlacup&se=2018-01-31T03:24:04Z&st=2018-01-30T19:24:04Z&spr=https,http&sig=IrkO1bVQCHcaKaTiJ5gilLSC5Wxtghu%2FJAeeY5HR%2BPU%3D
    ```

9. Vytvořte skupinu prostředků, která obsahuje prostředky centra událostí. Služba Event Hubs slouží k odesílání zpráv ze Service Fabric na server, na kterém jsou spuštěné prostředky ELK.

    ```bash
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]
    
    Example: az group create --location westus --name testeventhubsrg
    ```

10. Pomocí následujícího příkazu vytvořte prostředek služby Event Hubs. Postupujte podle zobrazených výzev a zadejte podrobnosti pro hodnoty namespaceName (název oboru názvů), eventHubName (název centra událostí), consumerGroupName (název skupiny příjemců), sendAuthorizationRuleName (název autorizačního pravidla pro odesílání) a receiveAuthorizationRuleName (název autorizačního pravidla pro příjem). 

    ```bash
    az group deployment create -g [RESOURCE-GROUP-NAME] --template-file eventhubsdeploy.json
    
    Example: 
    az group deployment create -g testeventhubsrg --template-file eventhubsdeploy.json
    Please provide string value for 'namespaceName' (? for help): testeventhubnamespace
    Please provide string value for 'eventHubName' (? for help): testeventhub
    Please provide string value for 'consumerGroupName' (? for help): testeventhubconsumergroup
    Please provide string value for 'sendAuthorizationRuleName' (? for help): sender
    Please provide string value for 'receiveAuthorizationRuleName' (? for help): receiver
    ```

    Zkopírujte obsah pole **output** ve výstupu JSON předchozího příkazu. Informace o odesilateli se použijí při vytváření clusteru Service Fabric. Název a klíč příjemce byste si měli uložit pro použití v dalším kurzu, kdy nakonfigurujete službu Logstash pro příjem zpráv z centra událostí. Následující objekt blob představuje příklad výstupu JSON:     
    
    ```json
    "outputs": {
        "receiver Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "receiver Name": {
            "type": "String",
            "value": "receiver"
        },
        "sender Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "sender Name": {
            "type": "String",
            "value": "sender"
        }
    }
    ```

11. Spuštěním skriptu *eventhubssastoken.py* vygenerujte adresu URL SAS pro prostředek služby Event Hubs, který jste vytvořili. Pomocí této adresy URL SAS bude cluster Service Fabric odesílat protokoly do služby Event Hubs. To znamená, že se k vygenerování této adresy URL použije zásada **sender** (odesilatel). Skript vrátí adresu URL SAS prostředku služby Event Hubs, která se použije v následujícím kroku:

    ```python
    python3 eventhubssastoken.py 'testeventhubs' 'testeventhubs' 'sender' '[PRIMARY-KEY]'
    ```

    Zkopírujte hodnotu pole **sr** ve vráceném kódu JSON. Hodnota pole **sr** je token SAS pro službu Event Hubs. Následující adresa URL je příkladem pole **sr**:

    ```bash
    https%3A%2F%2Ftesteventhubs.servicebus.windows.net%2Ftesteventhubs&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=<policy_name>
    ```

    Vaše adresa URL SAS pro službu Event Hubs má tuto strukturu: https://<namespacename>.servicebus.windows.net/<eventhubsname>?sr=<sastoken>. Například https://testeventhubs.servicebus.windows.net/testeventhubs?sr=https%3A%2F%2Ftesteventhubs.servicebus.windows.net%2Ftesteventhubs&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender.

12. Otevřete soubor *sfdeploy.parameters.json* a nahraďte následující obsah hodnotami z předchozích kroků. 

    ```
    "applicationDiagnosticsStorageAccountName": {
        "value": "teststorageaccount"
    },
    "applicationDiagnosticsStorageAccountSasToken": {
        "value": "[SAS-URL-STORAGE-ACCOUNT]"
    },
    "loggingEventHubSAS": {
        "value": "[SAS-URL-EVENT-HUBS]"
    }
    ```

13. Spuštěním následujícího příkazu vytvořte cluster Service Fabric.

    ```bash
    az sf cluster create --location 'westus' --resource-group 'testlinux' --template-file sfdeploy.json --parameter-file sfdeploy.parameters.json --secret-identifier <certificate_url_from_step4>
    ```

## <a name="deploy-your-application-to-the-cluster"></a>Nasazení aplikace do clusteru

1. Před nasazením aplikace je potřeba do souboru *Voting/VotingApplication/ApplicationManifest.xml* přidat následující fragment kódu. Pole **X509FindValue** obsahuje kryptografický otisk vrácený z kroku 4 v části **Vytvoření clusteru Service Fabric v Azure**. Tento fragment kódu je vnořený do pole **ApplicationManifest** (kořenové pole). 

    ```xml
    <Certificates>
          <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[CERTIFICATE-THUMBPRINT]" />
    </Certificates>
    ```

2. Pokud chcete nasadit aplikaci do tohoto clusteru, musíte pomocí SFCTL navázat připojení ke clusteru. SFCTL pro připojení ke clusteru vyžaduje soubor PEM s veřejným i privátním klíčem. Proto spuštěním následujícího příkazu vygenerujte soubor PEM s veřejným i privátním klíčem. 

    ```bash
    openssl pkcs12 -in testservicefabric.westus.cloudapp.azure.com.pfx -out sfctlconnection.pem -nodes -passin pass:<password>
    ```

3. Spuštěním následujícího příkazu se připojte ke clusteru.

    ```bash
    sfctl cluster select --endpoint https://testlinuxcluster.westus.cloudapp.azure.com:19080 --pem sfctlconnection.pem --no-verify
    ```

4. Pokud chcete nasadit svou aplikaci, přejděte do složky *Voting/Scripts* a spusťte skript **install.sh**. 

    ```bash
    ./install.sh
    ```

5. Pokud chcete přejít do nástroje Service Fabric Explorer, otevřete oblíbený prohlížeč a zadejte do něj https://testlinuxcluster.westus.cloudapp.azure.com:19080. Z úložiště certifikátů zvolte certifikát, který chcete použít pro připojení k tomuto koncovému bodu. Pokud používáte počítač s Linuxem, k zobrazení nástroje Service Fabric Explorer je potřeba do Chrome importovat certifikáty vygenerované skriptem *new-service-fabric-cluster-certificate.sh*. Pokud používáte počítač Mac, musíte soubor PFX nainstalovat do své klíčenky. Všimněte si, že se vaše aplikace nainstalovala do clusteru. 

    ![SFX pro Javu v Azure](./media/service-fabric-tutorial-java-deploy-azure/sfxjavaonazure.png)

6. Pokud chcete přejít do své aplikace, zadejte https://testlinuxcluster.westus.cloudapp.azure.com:8080. 

    ![Hlasovací aplikace v Javě v Azure](./media/service-fabric-tutorial-java-deploy-azure/votingappjavaazure.png)

7. Pokud chcete aplikaci z clusteru odinstalovat, spusťte skript *uninstall.sh* ve složce **Scripts**. 

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření zabezpečeného clusteru s Linuxem v Azure 
> * Vytvoření prostředků potřebných pro monitorování pomocí ELK 
> * Volitelné: Použití Party Clusterů k vyzkoušení Service Fabric

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Nastavení monitorování a diagnostiky](service-fabric-tutorial-java-elk.md)
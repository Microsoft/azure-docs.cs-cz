---
title: Připojení k výpočetní instanci v Visual Studio Code (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se připojit k Azure Machine Learning výpočetní instanci v Visual Studio Code a spustit interaktivní Jupyter Notebook a vzdálené vývojové úlohy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 11/16/2020
ms.openlocfilehash: ccd56afc8c4ea7e236946fc6afa54e471203fe31
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065976"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Připojení k Azure Machine Learning výpočetní instance v Visual Studio Code (Preview)

V tomto článku se dozvíte, jak se připojit k instanci služby Azure Machine Learning COMPUTE pomocí Visual Studio Code.

[Instance služby Azure Machine Learning COMPUTE](concept-compute-instance.md) je plně spravovaná cloudová pracovní stanice pro odborníky přes data a poskytuje funkce pro správu a připravenost v podniku pro správce IT.

Existují dva způsoby, jak se můžete připojit k výpočetní instanci z Visual Studio Code:

* Vzdálený Jupyter Notebook Server. Tato možnost umožňuje nastavit výpočetní instanci jako server vzdálené Jupyter Notebook.
* [Visual Studio Code vzdáleného vývoje](https://code.visualstudio.com/docs/remote/remote-overview). Visual Studio Code vzdáleném vývoji můžete použít kontejner, vzdálený počítač nebo podsystém Windows pro Linux (WSL) jako plnohodnotné vývojové prostředí.

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Konfigurace výpočetní instance jako vzdáleného poznámkového serveru

Abyste mohli nakonfigurovat výpočetní instanci jako server vzdálené Jupyter Notebook, budete potřebovat několik požadavků:

* Rozšíření Azure Machine Learning Visual Studio Code. Další informace najdete v [Průvodci nastavením rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).
* Azure Machine Learning pracovní prostor. [Pomocí rozšíření Azure Machine Learning Visual Studio Code můžete vytvořit nový pracovní prostor](how-to-manage-resources-vscode.md#create-a-workspace) , pokud ho ještě nemáte.

Připojení k výpočetní instanci:

1. Otevřete Jupyter Notebook v Visual Studio Code.
1. Po načtení integrovaného poznámkového bloku vyberte **Jupyter Server**.

    > [!div class="mx-imgBorder"]
    > ![Rozevírací seznam pro spuštění Azure Machine Learning vzdáleného Jupyter Notebook serveru](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    Případně můžete také použít paletu příkazů:

    1. Otevřete paletu příkazů tak, že na řádku nabídek vyberete **zobrazit > paleta příkazů** .
    1. Do textového pole zadejte `Azure ML: Connect to Compute instance Jupyter server` .

1. Vyberte `Azure ML Compute Instances` ze seznamu možností serveru Jupyter.
1. Vyberte své předplatné ze seznamu předplatných. Pokud jste dříve nakonfigurovali výchozí pracovní prostor Azure Machine Learning, tento krok se přeskočí.
1. Vyberte svůj pracovní prostor.
1. Ze seznamu vyberte svou výpočetní instanci. Pokud ho nemáte, vyberte **vytvořit novou instanci služby Azure výpočetní prostředky služby ml** a vytvořte ji podle pokynů.
1. Změny se projeví až po opětovném načtení Visual Studio Code.
1. Otevřete Jupyter Notebook a spusťte buňku.

> [!IMPORTANT]
> Aby bylo možné navázat spojení, je **nutné** spustit buňku.

V tuto chvíli můžete pokračovat v práci s buňkami v Jupyter Notebook.

> [!TIP]
> Můžete také pracovat se soubory skriptu Pythonu (. py), které obsahují Jupyter buňky kódu. Další informace najdete v tématu [interaktivní dokumentace k Visual Studio Code Pythonu](https://code.visualstudio.com/docs/python/jupyter-support-py).

## <a name="configure-compute-instance-remote-development"></a>Konfigurace vzdáleného vývoje instance COMPUTE

Pro plně funkční vzdálené vývojové prostředí budete potřebovat několik požadavků:

* [Visual Studio Code vzdálené rozšíření SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh).
* Výpočetní instance s povoleným SSH Další informace najdete v [Průvodci vytvořením výpočetní instance](how-to-create-manage-compute-instance.md).

> [!NOTE]
> Na platformách systému Windows je nutné [nainstalovat klienta ssh kompatibilního s OpenSSH](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) , pokud ještě není přítomen. Výstup do systému Windows není podporován, protože příkaz SSH musí být v cestě.

### <a name="get-the-ip-and-ssh-port-for-your-compute-instance"></a>Získání portu IP a SSH pro vaši instanci služby COMPUTE

1. Přejít na Azure Machine Learning studia na adrese https://ml.azure.com/ .
2. Vyberte svůj [pracovní prostor](concept-workspace.md).
1. Klikněte na kartu **výpočetní instance** .
1. Ve sloupci **identifikátor URI aplikace** klikněte na odkaz **SSH** výpočetní instance, kterou chcete použít jako vzdálené výpočty. 
1. V dialogu si poznamenejte IP adresu a port SSH. 
1. Uložte privátní klíč do adresáře ~/.SSH/na místním počítači. Otevřete například editor nového souboru a vložte klíč do: 

   **Linux**:

   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**:

   ```cmd
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa
   ```

   Privátní klíč bude vypadat trochu takto:

   ```text
   -----BEGIN RSA PRIVATE KEY-----

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw==

   -----END RSA PRIVATE KEY-----
   ```

1. Změňte oprávnění na soubor, abyste se ujistili, že soubor můžete číst jenom vy.  

   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa
   ```

### <a name="add-instance-as-a-host"></a>Přidat instanci jako hostitele

`~/.ssh/config`V editoru otevřete soubor (Linux) nebo `C:\Users<username>.ssh\config` (Windows) a přidejte novou položku podobnou následujícímu obsahu:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa
```

Zde jsou některé podrobnosti o polích:

|Pole|Description|
|----|---------|
|Hostitel|Použijte libovolné zkratky, které chcete použít pro instanci služby Compute. |
|Název hostitele|Toto je IP adresa instance Compute. |
|Port|Toto je port zobrazený v dialogovém okně SSH výše. |
|User|To je nutné `azureuser` |
|IdentityFile|Měl by odkazovat na soubor, kam jste uložili soukromý klíč. |

Nyní byste měli být schopni získat SSH na vaši instanci služby COMPUTE pomocí zkratky, kterou jste použili výše `ssh azmlci1` .

### <a name="connect-vs-code-to-the-instance"></a>Připojit VS Code k instanci

1. Kliknutím na ikonu Remote-SSH z panelu Visual Studio Code aktivity zobrazíte konfigurace SSH.

1. Klikněte pravým tlačítkem na konfiguraci hostitele SSH, kterou jste právě vytvořili.

1. **V aktuálním okně vyberte připojit k hostiteli**. 

Odtud jste na této službě naprosto pracovali na výpočetní instanci a teď můžete upravovat, ladit, používat Git, používat rozšíření atd. – stejně jako u místních Visual Studio Code.

## <a name="next-steps"></a>Další kroky

Teď, když jste nastavili Visual Studio Code vzdálené, můžete použít výpočetní instanci jako vzdálenou výpočetní prostředky z Visual Studio Code k [interaktivnímu ladění kódu](how-to-debug-visual-studio-code.md).

[Kurz: analýza prvního modelu ml](tutorial-1st-experiment-sdk-train.md) ukazuje, jak používat výpočetní instanci s integrovaným poznámkovým blokem.

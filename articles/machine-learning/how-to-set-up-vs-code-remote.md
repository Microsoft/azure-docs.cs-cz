---
title: 'Interaktivní ladění: VS Code & ML výpočetních instancí'
titleSuffix: Azure Machine Learning
description: Nastavte VS Code vzdálené na interaktivní ladění kódu pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 19d4b6e7994450eacacab51e9a01cbf58eab803a
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433134"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Interaktivně se ladit na Azure Machine Learning výpočetní instanci s VS Code Vzdálená

V tomto článku se dozvíte, jak nastavit Visual Studio Code vzdáleně na instanci služby Azure Machine Learning COMPUTE, abyste mohli **interaktivně ladit kód** z vs Code. 

+ [Instance služby Azure Machine Learning COMPUTE](concept-compute-instance.md) je plně spravovaná cloudová pracovní stanice pro odborníky přes data a poskytuje funkce pro správu a připravenost v podniku pro správce IT. 


+ [Visual Studio Code vzdálené](https://code.visualstudio.com/docs/remote/remote-overview) Vývoj umožňuje používat kontejner, vzdálený počítač nebo podsystém Windows pro Linux (WSL) jako plnohodnotné vývojové prostředí. 

## <a name="prerequisite"></a>Požadavek  

Na platformách systému Windows je nutné [nainstalovat klienta ssh kompatibilního s OpenSSH](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) , pokud ještě není přítomen. 

> [!Note]
> Výstup do systému Windows není podporován, protože příkaz SSH musí být v cestě. 

## <a name="get-ip-and-ssh-port"></a>Získat port IP a SSH 

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
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   Privátní klíč bude vypadat trochu takto:
   ```
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

## <a name="add-instance-as-a-host"></a>Přidat instanci jako hostitele 

`~/.ssh/config`V editoru otevřete soubor (Linux) nebo `C:\Users<username>.ssh\config` (Windows) a přidejte novou položku podobnou této:

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
|HostName|Toto je IP adresa instance Compute. |
|Port|Toto je port zobrazený v dialogovém okně SSH výše. |
|Uživatel|To je nutné `azureuser` |
|IdentityFile|Měl by odkazovat na soubor, kam jste uložili soukromý klíč. |

Nyní byste měli být schopni získat SSH na vaši instanci služby COMPUTE pomocí zkratky, kterou jste použili výše `ssh azmlci1` . 

## <a name="connect-vs-code-to-the-instance"></a>Připojit VS Code k instanci 

1. [Nainstalujte Visual Studio Code](https://code.visualstudio.com/).

1. [Nainstalujte vzdálené rozšíření SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Kliknutím na ikonu Remote-SSH na levé straně zobrazíte konfigurace SSH.

1. Klikněte pravým tlačítkem na konfiguraci hostitele SSH, kterou jste právě vytvořili.

1. **V aktuálním okně vyberte připojit k hostiteli**. 

Odtud jste na této službě naprosto pracovali na výpočetní instanci a teď můžete upravovat, ladit, používat Git, používat rozšíření atd. – stejně jako u místních Visual Studio Code. 

## <a name="next-steps"></a>Další kroky

Teď, když jste nastavili Visual Studio Code vzdálené, můžete použít výpočetní instanci jako vzdálenou výpočetní prostředky z Visual Studio Code k interaktivnímu ladění kódu. 

[Kurz: analýza prvního modelu ml](tutorial-1st-experiment-sdk-train.md) ukazuje, jak používat výpočetní instanci s integrovaným poznámkovým blokem.
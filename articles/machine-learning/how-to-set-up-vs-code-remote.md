---
title: 'Interaktivní ladění: Výpočetní instance VS Code & ML'
titleSuffix: Azure Machine Learning
description: Nastavte vzdálené vs kód interaktivně ladit kód s Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 1999d29db21f820fbcdbca08f2258b657673be3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169758"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Interaktivní ladění na výpočetní instanci Azure Machine Learning Compute s dálkovým ovládáním kódu VS

V tomto článku se dozvíte, jak nastavit Visual Studio Code Remote na Azure Machine Learning Compute Instance, takže můžete **interaktivně ladit kód** z VS Code. 

+ [Azure Machine Learning Compute Instance](concept-compute-instance.md) je plně spravovaná cloudová pracovní stanice pro datové vědce a poskytuje možnosti správy a připravenosti podniku pro správce IT. 


+ [Vzdálený kód visual studia](https://code.visualstudio.com/docs/remote/remote-overview) Vývoj umožňuje používat kontejner, vzdálený počítač nebo podsystém Windows pro Linux (WSL) jako plnohodnotné vývojové prostředí. 

## <a name="prerequisite"></a>Požadavek  

Na platformách Windows je nutné [nainstalovat klienta SSH kompatibilního s OpenSSH,](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) pokud ještě není k dispozici. 

> [!Note]
> PuTTY není podporována v systému Windows, protože příkaz ssh musí být v cestě. 

## <a name="get-ip-and-ssh-port"></a>Získání portu IP a SSH 

1. Přejděte do studia Azure https://ml.azure.com/Machine Learning studio na .

2. Vyberte [pracovní prostor](concept-workspace.md).
1. Klikněte na kartu **Výpočetní instance.**
1. Ve sloupci **Identifikátor URI aplikace** klikněte na odkaz **SSH** výpočetní instance, kterou chcete použít jako vzdálený výpočetní výkon. 
1. V dialogovém okně si poznamenejte IP adresu a port SSH. 
1. Uložte svůj soukromý klíč do adresáře ~/.ssh/ v místním počítači; otevřete například editor pro nový soubor a vložte klíč do: 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   Soukromý klíč bude vypadat poněkud takto:
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. Změňte oprávnění k souboru, abyste měli jistotu, že soubor můžete přečíst pouze vy.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>Přidání instance jako hostitele 

Otevřete `~/.ssh/config` soubor (Linux) nebo `C:\Users<username>.ssh\config` (Windows) v editoru a přidejte novou položku podobnou této:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

Zde některé podrobnosti o polích: 

|Pole|Popis|
|----|---------|
|Hostitel|Použijte pro výpočetní instanci jakoukoli těsnopis, který se vám líbí |
|HostName|Toto je IP adresa instance výpočetních prostředků. |
|Port|Toto je port zobrazený na ssh dialogu výše |
|Uživatel|To je třeba `azureuser` |
|Soubor identity|Má ukazovat na soubor, do kterého jste uložili soukromý klíč. |

Nyní byste měli být schopni ssh na vaše výpočetní instance `ssh azmlci1`pomocí těsnopisu, který jste použili výše, . 

## <a name="connect-vs-code-to-the-instance"></a>Připojení kódu VS k instanci 

1. [Nainstalujte kód sady Visual Studio](https://code.visualstudio.com/).

1. [Nainstalujte vzdálené rozšíření SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Kliknutím na ikonu Remote-SSH vlevo zobrazíte konfigurace SSH.

1. Klikněte pravým tlačítkem myši na konfiguraci hostitele SSH, kterou jste právě vytvořili.

1. V **aktuálním okně vyberte Možnost Připojit k hostiteli**. 

Od této chvíle zcela pracujete na výpočetní instanci a teď můžete upravovat, ladit, používat git, používat rozšíření atd. 

## <a name="next-steps"></a>Další kroky

Teď, když jste nastavili Visual Studio Code Remote, můžete použít výpočetní instance jako vzdálené výpočetní prostředky z kódu Sady Visual Studio interaktivně ladit kód. 

[Kurz: Trénování prvního modelu ML](tutorial-1st-experiment-sdk-train.md) ukazuje, jak používat výpočetní instanci s integrovaným poznámkovým blokem.
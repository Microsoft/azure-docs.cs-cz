---
title: Vytvoření serveru Jenkins v Azure
description: Instalace Jenkinse na virtuálním počítači Azure s Linuxem ze šablony řešení Jenkins a sestavení ukázkové aplikace v Javě.
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: c9f86ab2536d3c598bb8c7084524395b41f18db0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635454"
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Vytvoření serveru Jenkins na virtuálním počítači Azure s Linuxem pomocí webu Azure Portal

V tomto rychlém startu se dozvíte, jak na virtuálním počítači s Ubuntu Linuxem nainstalovat [Jenkinse](https://jenkins.io) s nakonfigurovanými nástroji a moduly plug-in pro práci s Azure. Až budete hotovi, budete mít v Azure spuštěný server Jenkins sestavující ukázkovou aplikaci v Javě z [GitHubu](https://github.com).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure
* Přístup k SSH na příkazovém řádku vašeho počítače (například prostředí Bash nebo nástroj [PuTTY](http://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Vytvoření virtuálního počítače s Jenkinsem ze šablony řešení
Jenkins podporuje model, ve kterém Jenkins delegáti server fungovat pro jednu nebo více agentů umožňuje jednotnou instalaci Jenkinse hostovat velký počet projektů nebo poskytují různá prostředí potřebné pro sestavení nebo zkoušky. Kroky v této části Průvodce vás provede instalací a konfigurací serveru Jenkins v Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-jenkins"></a>Připojení k Jenkinsu

Přejděte ke svému virtuálnímu počítači (například http://jenkins2517454.eastus.cloudapp.azure.com/) ve webovém prohlížeči. Konzola Jenkinse není přístupná přes nezabezpečený protokol HTTP, proto se na stránce zobrazí pokyny pro zabezpečený přístup ke konzole Jenkinse z vašeho počítače pomocí tunelu SSH.

![Odemknutí Jenkinse](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

Nastavte tunel z příkazového řádku s použitím příkazu `ssh` uvedeného na stránce, ve kterém nahradíte `username` názvem uživatele s právy pro správu virtuálního počítače, který jste zvolili dříve při nastavování virtuálního počítače ze šablony řešení.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

Po spuštění tunelu přejděte na http://localhost:8080/ na místním počítači. 

Získejte počáteční heslo spuštěním následujícího příkazu v příkazovém řádku, zatímco jste připojeni k virtuálnímu počítači s Jenkinsem prostřednictvím SSH.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Pomocí tohoto počátečního hesla poprvé odemkněte řídicí panel Jenkinse.

![Odemknutí Jenkinse](./media/install-jenkins-solution-template/jenkins-unlock.png)

Na další stránce vyberte **Install suggested plugins** (Nainstalovat doporučené moduly plug-in) a pak vytvořte uživatele s právy pro správu Jenkinse, který bude sloužit pro přístup k řídicímu panelu Jenkinse.

![Jenkins je připraven!](./media/install-jenkins-solution-template/jenkins-welcome.png)

Server Jenkins je teď připravený sestavovat kód.

## <a name="create-your-first-job"></a>Vytvoření první úlohy

V konzole Jenkinse vyberte **Create new jobs** (Vytvořit nové úlohy), zadejte název **mySampleApp**, vyberte **Freestyle project** (Volný projekt) a pak vyberte **OK**.

![Vytvoření nové úlohy](./media/install-jenkins-solution-template/jenkins-new-job.png) 

Vyberte kartu **Source Code Management** (Správa zdrojového kódu), povolte **Git** a do pole **Repository URL** (Adresa URL úložiště) zadejte následující adresu URL: `https://github.com/spring-guides/gs-spring-boot.git`.

![Definování úložiště Git](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

Vyberte kartu **Build** (Sestavení) a pak vyberte **Add build step** (Přidat krok sestavení) a **Invoke Gradle script** (Vyvolání skriptu Gradle). Vyberte **Use Gradle Wrapper** (Použít obálku Gradle) a pak zadejte `complete` do pole **Wrapper location** (Umístění obálky) a `build` do pole **Tasks** (Úlohy).

![Použití obálky Gradle k sestavení](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

Vyberte **Advanced...** (Upřesnit...) a pak zadejte `complete` do pole **Root Build script** (Kořenový skript sestavení). Vyberte **Uložit**.

![Nastavení upřesňujících nastavení v kroku sestavení obálky Gradle](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Sestavení kódu

Vyberte **Build Now** (Sestavit). Kód se zkompiluje a ukázková aplikace se zabalí do balíčku. Po dokončení sestavení vyberte odkaz **Workspace** (Pracovní prostor) pro projekt.

![Přechod do pracovního prostoru a získání souboru JAR ze sestavení](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

Přejděte do umístění `complete/build/libs` a ujistěte se, že je tam soubor `gs-spring-boot-0.1.0.jar`, abyste ověřili, že sestavení proběhlo úspěšně. Váš server Jenkins je teď připravený k sestavování vašich vlastních projektů v Azure.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání virtuálních počítačů Azure jako agentů Jenkinse](jenkins-azure-vm-agents.md)

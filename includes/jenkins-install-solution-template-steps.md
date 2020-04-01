---
author: tomarcher
ms.service: jenkins
ms.topic: include
ms.date: 03/03/2020
ms.author: tarcher
ms.openlocfilehash: e9b8ad7a7fcc499f8760b56e6a737be8a6a9e06c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79200009"
---
## <a name="prerequisites"></a>Požadavky

* Předplatné Azure
* Přístup k SSH na příkazovém řádku vašeho počítače (například prostředí Bash nebo nástroj [PuTTY](https://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Vytvoření virtuálního počítače s Jenkinsem ze šablony řešení
Jenkins podporuje model, ve kterém server Jenkins deleguje práci do jednoho nebo několika agentů, což umožňuje, aby jedna instalace Jenkinse hostovala velký počet projektů nebo poskytovala různá prostředí potřebná pro sestavení nebo testy. Kroky v této části vás provedou instalací a konfigurací serveru Jenkins v Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-jenkins"></a>Připojení k Jenkinsu

Přejděte do virtuálního počítače `http://jenkins2517454.eastus.cloudapp.azure.com/`(například) ve webovém prohlížeči. Konzola Jenkinse není přístupná přes nezabezpečený protokol HTTP, proto se na stránce zobrazí pokyny pro zabezpečený přístup ke konzole Jenkinse z vašeho počítače pomocí tunelu SSH.

![Odemknutí Jenkinse](./media/jenkins-install-solution-template-steps/jenkins-ssh-instructions.png)

Nastavte tunel z příkazového řádku s použitím příkazu `ssh` uvedeného na stránce, ve kterém nahradíte `username` názvem uživatele s právy pro správu virtuálního počítače, který jste zvolili dříve při nastavování virtuálního počítače ze šablony řešení.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

Po spuštění tunelu přejděte na místním počítači na adresu `http://localhost:8080/`. 

Získejte počáteční heslo spuštěním následujícího příkazu v příkazovém řádku, zatímco jste připojeni k virtuálnímu počítači s Jenkinsem prostřednictvím SSH.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Pomocí tohoto počátečního hesla poprvé odemkněte řídicí panel Jenkinse.

![Odemknutí Jenkinse](./media/jenkins-install-solution-template-steps/jenkins-unlock.png)

Na další stránce vyberte **Install suggested plugins** (Nainstalovat doporučené moduly plug-in) a pak vytvořte uživatele s právy pro správu Jenkinse, který bude sloužit pro přístup k řídicímu panelu Jenkinse.

![Jenkins je připraven!](./media/jenkins-install-solution-template-steps/jenkins-welcome.png)

Server Jenkins je teď připravený sestavovat kód.

## <a name="create-your-first-job"></a>Vytvoření první úlohy

V konzole Jenkinse vyberte **Create new jobs** (Vytvořit nové úlohy), zadejte název **mySampleApp**, vyberte **Freestyle project** (Volný projekt) a pak vyberte **OK**.

![Vytvoření nové úlohy](./media/jenkins-install-solution-template-steps/jenkins-new-job.png) 

Vyberte kartu **Source Code Management** (Správa zdrojového kódu), povolte **Git** a do pole **Repository URL** (Adresa URL úložiště) zadejte následující adresu URL: `https://github.com/spring-guides/gs-spring-boot.git`.

![Definování úložiště Git](./media/jenkins-install-solution-template-steps/jenkins-job-git-configuration.png) 

Vyberte kartu **Build** (Sestavení) a pak vyberte **Add build step** (Přidat krok sestavení) a **Invoke Gradle script** (Vyvolání skriptu Gradle). Vyberte **Use Gradle Wrapper** (Použít obálku Gradle) a pak zadejte `complete` do pole **Wrapper location** (Umístění obálky) a `build` do pole **Tasks** (Úlohy).

![Použití obálky Gradle k sestavení](./media/jenkins-install-solution-template-steps/jenkins-job-gradle-config.png) 

Vyberte **Advanced** (Upřesnit) a pak zadejte `complete` do pole **Root Build script** (Kořenový skript sestavení). Vyberte **Uložit**.

![Nastavení upřesňujících nastavení v kroku sestavení obálky Gradle](./media/jenkins-install-solution-template-steps/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Sestavení kódu

Vyberte **Build Now** (Sestavit). Kód se zkompiluje a ukázková aplikace se zabalí do balíčku. Po dokončení sestavení vyberte odkaz **Workspace** (Pracovní prostor) pro projekt.

![Přechod do pracovního prostoru a získání souboru JAR ze sestavení](./media/jenkins-install-solution-template-steps/jenkins-access-workspace.png) 

Přejděte do umístění `complete/build/libs` a ujistěte se, že je tam soubor `gs-spring-boot-0.1.0.jar`, abyste ověřili, že sestavení proběhlo úspěšně. Váš server Jenkins je teď připravený k sestavování vašich vlastních projektů v Azure.

## <a name="troubleshooting-the-jenkins-solution-template"></a>Řešení potíží s šablonou řešení Jenkinse

Pokud při práci s šablonou řešení Jenkinse narazíte na nějaké chyby, založte problém v [úložišti Jenkinse na GitHubu](https://github.com/azure/jenkins/issues).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání virtuálních počítačů Azure jako agentů Jenkinse](/azure/jenkins/jenkins-azure-vm-agents)
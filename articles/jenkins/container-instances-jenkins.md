---
title: Jenkins staví na instanci kontejneru
description: Zjistěte, jak nakonfigurovat server Jenkinspro spouštění úloh sestavení na vyžádání v instanci kontejnerů Azure
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: 8bb84895fb581053248fbad326ea7b2c8d1873a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617967"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Použití instancí kontejnerů Azure jako agenta sestavení Jenkinse

Azure Container Instances (ACI) poskytuje na vyžádání, burstable a izolované prostředí pro spouštění kontejnerizovaných úloh. Z důvodu těchto atributů ACI je skvělá platforma pro spuštění Jenkins sestavení úloh ve velkém měřítku. Tento článek vás provede nasazenía pomocí serveru Jenkins, který je předem nakonfigurovaný s ACI jako cíl sestavení.

Další informace o instancích kontejnerů Azure najdete v tématu [O instanci kontejnerů Azure](../container-instances/container-instances-overview.md).

## <a name="deploy-a-jenkins-server"></a>Nasazení serveru Jenkins

1. Na webu Azure Portal vyberte **Vytvořit prostředek** a vyhledejte **Jenkinse**. Vyberte nabídku Jenkins u vydavatele **společnosti Microsoft**a pak vyberte **Vytvořit**.

2. Do formuláře **Základy** zadejte následující informace a pak vyberte **OK**.

   - **Název**: Zadejte název nasazení Jenkinse.
   - **Uživatelské jméno**: Zadejte jméno správce virtuálního počítače Jenkins.
   - **Typ ověřování**: Pro ověřování doporučujeme veřejný klíč SSH. Pokud vyberete tuto možnost, vložte do veřejného klíče SSH, který se použije pro přihlášení k virtuálnímu počítači Jenkins.
   - **Předplatné**: Vyberte předplatné Azure.
   - **Skupina prostředků:** Vytvořte skupinu prostředků nebo vyberte stávající.
   - **Umístění**: Vyberte umístění serveru Jenkins.

   ![Základní nastavení pro nasazení portálu Jenkins](./media/container-instances-jenkins/jenkins-portal-01.png)

3. Ve formuláři **Další nastavení** vyplňte následující položky:

   - **Velikost**: Vyberte příslušnou možnost velikosti pro váš virtuální počítač Jenkins.
   - **Typ disku virtuálního počítače**: Zadejte pro server Jenkins disk **HDD** (pevný disk) nebo **SSD** (ssd disk).
   - **Virtuální síť**: Pokud chcete změnit výchozí nastavení, vyberte šipku.
   - **Podsítě**: Vyberte šipku, ověřte informace a vyberte **OK**.
   - **Veřejná IP adresa**: Vyberte šipku, chcete-li veřejné adrese IP přidělit vlastní název, nakonfigurovat skladovou položku a nastavit metodu přiřazení.
   - **Popisek názvu domény**: Zadejte hodnotu pro vytvoření plně kvalifikované adresy URL virtuálního počítače Jenkins.
   - **Typ vydání Jenkins**: Vyberte požadovaný typ verze z možností: **LTS**, **Týdenní sestavení**nebo Azure **Verified**.

   ![Další nastavení pro nasazení portálu Jenkins](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Pro integraci instančního objektu vyberte **Auto(MSI),** aby [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) automaticky vytvořily ověřovací identitu pro instanci Jenkinse. Výběrem **možnosti Ručně** zadejte vlastní pověření zaregistrovaný objekt servisu.

5. Cloudoví agenti konfigurují cloudovou platformu pro jenkinsské úlohy. V zájmu tohoto článku vyberte **ACI**. S cloudovým agentem ACI je každá úloha sestavení Jenkinse spuštěna v instanci kontejneru.

   ![Nastavení integrace cloudu pro nasazení portálu Jenkins](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Až budete hotovi s nastavením integrace, vyberte **OK**a pak v souhrnu ověření znovu vyberte **OK.** V souhrnu **podmínek použití** vyberte **Vytvořit.** Nasazení serveru Jenkins trvá několik minut.

## <a name="configure-jenkins"></a>Konfigurace Jenkinse

1. Na webu Azure Portal přejděte na skupinu prostředků Jenkins, vyberte virtuální počítač Jenkins a poznamenejte si název DNS.

   ![Název DNS v podrobnostech o virtuálním počítači Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Přejděte na název DNS virtuálního soudu Jenkins a zkopírujte vrácený řetězec SSH.

   ![Přihlašovací pokyny Jenkinse s řetězcem SSH](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Otevřete terminálovou relaci ve vývojovém systému a vložte řetězec SSH od posledního kroku. Aktualizujte `username` na uživatelské jméno, které jste zadali při nasazení serveru Jenkins.

4. Po připojení relace načtěte počáteční heslo správce následujícím příkazem:

   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Ponechte relaci SSH a tunelové `http://localhost:8080` propojení a přejděte do aplikace v prohlížeči. Vložte do pole počáteční heslo správce a pak vyberte **Pokračovat**.

   ![Obrazovka "Odemknout Jenkinse" s rámečkem pro heslo správce](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Chcete-li nainstalovat všechny doporučené moduly plug-in Jenkins, vyberte **možnost Instalovat navrhované pluginy.**

   ![Obrazovka "Přizpůsobit Jenkinse" s vybranou možností "Instalovat navrhované pluginy"](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Vytvořte uživatelský účet správce. Tento účet se používá pro přihlášení a práci s vaší instancí Jenkinse.

   ![Obrazovka "Vytvořit prvního uživatele správce" s vyplněnými přihlašovacími údaji](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Vyberte **Uložit a dokončit**a pak k dokončení konfigurace vyberte Začít používat **Jenkinse.**

Jenkins je teď nakonfigurovaný a připravený k vytváření a nasazování kódu. V tomto příkladu jednoduché java aplikace se používá k předvedení Jenkins sestavení na Azure Container Instances.

## <a name="create-a-build-job"></a>Vytvoření úlohy sestavení

Úloha sestavení Jenkinse se teď vytvoří, aby demonstrovala Jenkinsova sestavení na instanci kontejneru Azure.

1. Vyberte **Nová položka**, pojmenujte projekt sestavení, **například aci-demo**, vyberte **freestyle projekt**a vyberte **OK**.

   ![Pole pro název úlohy sestavení a seznam typů projektů](./media/container-instances-jenkins/jenkins-new-job.png)

2. V **části Obecné**zkontrolujte, zda je vybrána možnost **Omezit, kde lze tento projekt spustit.** Zadejte **linux** pro výraz popisku. Tato konfigurace zajišťuje, že tato úloha sestavení běží v cloudu ACI.

   ![Karta Obecné s podrobnostmi o konfiguraci](./media/container-instances-jenkins/jenkins-job-01.png)

3. V části **Build**vyberte **Add build step** a select Execute **Shell**. Zadejte `echo "aci-demo"` jako příkaz.

   ![Karta "Sestavení" s výběry pro krok sestavení](./media/container-instances-jenkins/jenkins-job-02.png)

5. Vyberte **Uložit**.

## <a name="run-the-build-job"></a>Spuštění úlohy sestavení

Chcete-li otestovat úlohu sestavení a sledovat instance kontejneru Azure jako platformu sestavení, ručně spusťte sestavení.

1. Chcete-li spustit úlohu sestavení, vyberte **možnost Sestavit nyní.** Trvá několik minut, než začne úloha. Měli byste vidět stav, který je podobný následujícímu obrázku:

   ![Informace o historii sestavení se stavem úlohy](./media/container-instances-jenkins/jenkins-job-status.png)

2. Když je úloha spuštěná, otevřete portál Azure a podívejte se na skupinu prostředků Jenkinse. Měli byste vidět, že byla vytvořena instance kontejneru. Jenkinsova úloha je spuštěna uvnitř této instance.

   ![Instance kontejneru ve skupině prostředků](./media/container-instances-jenkins/jenkins-aci.png)

3. Jako Jenkins spustí více úloh, než je nakonfigurovaný počet Jenkins ových vykonavatelů (výchozí 2), jsou vytvořeny více instancí kontejneru.

   ![Nově vytvořené instance kontejneru](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Po dokončení všech úloh sestavení jsou instance kontejneru odebrány.

   ![Skupina prostředků s odebranými instancemi kontejneru](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Řešení potíží s modulem plug-in Jenkinse

Pokud v modulech plug-in Jenkinse narazíte na nějaké chyby, založte problém na stránce [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní komponentu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Průběžná integrace a doručování do služby Azure App Service](java-deploy-webapp-tutorial.md)
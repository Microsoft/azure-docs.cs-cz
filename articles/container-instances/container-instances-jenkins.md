---
title: Použití Azure Container Instances jako Jenkins agenta sestavení
description: Zjistěte, jak pomocí Azure Container Instances, jak agenta sestavení Jenkinse.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/13/2018
ms.author: marsma
ms.openlocfilehash: d8ac5850a61c1dec9daa508236ef56836876c3fe
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072071"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Použití Azure Container Instances jako Jenkins agenta sestavení

Azure Container Instances (ACI) poskytuje na vyžádání, zvládáním výkonových špiček a izolované prostředí pro spouštění kontejnerizovaných úloh. Z důvodu tyto atributy ACI díky skvělým platformu pro spuštění úlohy sestavení Jenkinse ve velkém měřítku. Tento článek vás provede nasazením a pomocí ACI serveru Jenkins, který je předem nakonfigurovaný jako cíl sestavení.

Další informace o službě Azure Container Instances, naleznete v tématu [o službě Azure Container Instances][about-aci].

## <a name="deploy-a-jenkins-server"></a>Nasazení serveru Jenkins

1. Na webu Azure Portal, vyberte **vytvořit prostředek** a vyhledejte **Jenkins**. Vyberte nabídky Jenkinse s vydavatele **Microsoft**a pak vyberte **vytvořit**.

2. Zadejte následující informace **Základy** formulář a potom vyberte **OK**.

   - **Název**: Zadejte název pro nasazení Jenkinse.
   - **Uživatelské jméno**: Zadejte název pro uživatele s rolí správce Jenkinse virtuálního počítače.
   - **Typ ověřování**: doporučujeme veřejný klíč SSH pro ověřování. Pokud vyberete tuto možnost, vložte veřejný klíč SSH použitého pro přihlášení k virtuálnímu počítači Jenkinse.
   - **Předplatné:** Vyberte předplatné Azure.
   - **Skupina prostředků:** Vytvořte skupinu prostředků nebo vyberte stávající.
   - **Umístění**: Vyberte umístění pro Jenkins server.

   ![Základní nastavení pro nasazení portálu Jenkinse](./media/container-instances-jenkins/jenkins-portal-01.png)

3. Na **další nastavení** formuláře, dokončete následující položky:

   - **Velikost**: Vyberte možnost příslušné nastavení velikosti pro virtuální počítač Jenkinse.
   - **Typ disku virtuálního počítače**: Zadejte, jestli **HDD** (jednotku pevného disku) nebo **SSD** (jednotky SSD) pro Jenkins server.
   - **Virtuální síť**: vyberte šipku, pokud chcete upravit výchozí nastavení.
   - **Podsítě**: vyberte šipku, zkontrolujte zadané informace a vyberte **OK**.
   - **Veřejná IP adresa**: vyberte šipku a zadejte veřejnou IP adresu vlastní název, skladovou Položku konfigurace a nastavte jako metodu přiřazení.
   - **Popisek názvu domény**: Zadejte hodnotu vytvořit plně kvalifikovanou adresu URL pro virtuální počítač Jenkinse.
   - **Typ verze Jenkins**: Vyberte typ požadované verze z možností: **LTS**, **týdně sestavení**, nebo **Azure ověřit**.

   ![Další nastavení pro nasazení portálu Jenkinse](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Integrace instančního objektu služby, vyberte **Auto(MSI)** mít [identita spravované služby Azure] [ managed-service-identity] automaticky vytvořit identitu ověřování pro Jenkinse instance. Vyberte **ruční** poskytnout vlastní pověření instančního objektu.

5. Agenti cloud nakonfigurovat cloudové platformy pro úlohy sestavení Jenkinse. Pro účely tohoto článku, vyberte **ACI**. S agentem ACI cloudu Každá úloha sestavení Jenkinse běží v instanci kontejneru.

   ![Nastavení integrace cloudu pro nasazení portálu Jenkinse](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Až dokončíte nastavení integrace, vyberte **OK**a pak vyberte **OK** znovu v souhrnu ověření. Vyberte **vytvořit** na **podmínky použití** souhrnu. Jenkins server trvá několik minut, než nasazení.

## <a name="configure-jenkins"></a>Konfigurace Jenkinse

1. Na webu Azure Portal přejděte do skupiny prostředků Jenkinse, vyberte virtuální počítač Jenkins a poznamenejte si název DNS.

   ![Název DNS najdete v podrobnostech o virtuálním počítači Jenkinse](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Přejděte na název DNS virtuálního počítače s Jenkinsem a zkopírujte řetězec vrácený SSH.

   ![Pokyny k přihlášení Jenkinse s řetězcem SSH](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Otevřete relaci Terminálové ve vývojovém systému a vložte řetězec SSH z posledního kroku. Aktualizace `username` na uživatelské jméno, které jste zadali při nasazení serveru Jenkins.

4. Po relaci je připojený, spusťte následující příkaz, který načte k počátečnímu heslu správce:

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Nechte relaci SSH a tunelové propojení systémem a přejděte na http://localhost:8080 v prohlížeči. Vložte k počátečnímu heslu správce do pole a pak vyberte **pokračovat**.

   !["Odemknutí Jenkinse" obrazovky pole pro heslo správce](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Vyberte **nainstalovat navrhované moduly plug-in** nainstalovat všechny doporučené moduly plug-in Jenkins.

   ![Obrazovka "Přizpůsobit Jenkins" s "Nainstalovat navrhované moduly plug-in" vybraná](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Vytvořte uživatelský účet správce. Tento účet slouží pro přihlášení k a práci s vaší instance Jenkinse.

   !["Vytvořit první uživatel s rolí správce" obrazovka, pomocí přihlašovacích údajů k vyplnění](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Vyberte **uložit a dokončit**a pak vyberte **začátku používání Jenkinse** a dokončete tak konfiguraci.

Jenkins je teď nakonfigurovaná a připravená k vytvoření a nasazení kódu. V tomto příkladu jednoduché aplikace v Javě slouží k předvedení buildu Jenkinse v Azure Container Instances.

## <a name="create-a-build-job"></a>Vytvoření úlohy sestavení

Úloha sestavení Jenkinse je vytvořena k předvedení sestavení Jenkins na instanci kontejneru Azure.

1. Vyberte **nová položka**, pojmenujte projekt sestavení, jako **aci-demo**vyberte **Freestyle project**a vyberte **OK**.

   ![Pole pro název úlohy sestavení a v seznamu typů projektů](./media/container-instances-jenkins/jenkins-new-job.png)

2. V části **Obecné**, ujistěte se, že **omezit, kde lze tento projekt spustit** zaškrtnuto. Zadejte **linux** pro výraz popisku. Tato konfigurace zajistí, že tato úloha sestavení běží v cloudu ACI.

   !["Obecné" karta se podrobnosti o konfiguraci](./media/container-instances-jenkins/jenkins-job-01.png)

3. V části **sestavení**vyberte **přidat krok sestavení** a vyberte **spustit prostředí**. Zadejte `echo "aci-demo"` jako příkaz.

   !["Sestavení" kartu s výběry kroku sestavení](./media/container-instances-jenkins/jenkins-job-02.png)

5. Vyberte **Uložit**.

## <a name="run-the-build-job"></a>Spuštění úlohy sestavení

Chcete-li otestovat úlohu sestavení a podívejte se Azure Container Instances jako platformu sestavení, ručně spusťte sestavení.

1. Vyberte **Build Now** úlohu sestavení a spuštění. Trvá několik minut, než se spuštění úlohy. Byste měli vidět stav, který je podobný jako na následujícím obrázku:

   !["Sestavení historie" informace o stavu úlohy](./media/container-instances-jenkins/jenkins-job-status.png)

2. Když úloha běží, otevřete na webu Azure portal a podívejte se na skupinu prostředků Jenkinse. Měli byste vidět, že se vytvořil instanci kontejneru. Úlohy Jenkinse běží uvnitř této instance.

   ![Instance kontejnerů ve skupině prostředků](./media/container-instances-jenkins/jenkins-aci.png)

3. Jenkins běží víc úloh než nakonfigurovaného počtu prováděcích procesů Jenkins (výchozí 2), se vytvoří víc instancí kontejneru.

   ![Nově vytvořená instance kontejnerů](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Po dokončení všech úloh sestavení instance kontejnerů se odeberou.

   ![Skupina prostředků se službou container instances odebrat](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>Další postup

Další informace o systému Jenkins v Azure najdete v tématu [Azure a Jenkinse][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md
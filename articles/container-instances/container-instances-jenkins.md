---
title: Použití Azure kontejner instancí jako volaných sestavení agenta
description: Další informace o použití instancí kontejnerů Azure jako volaných sestavení agenta.
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/20/2018
ms.author: nepeters
ms.openlocfilehash: 4df230c8306a3876e94a5e9ada5e7408f134ba26
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Použití Azure kontejner instancí jako volaných sestavení agenta

Instance Azure kontejneru (ACI) poskytuje na vyžádání, burstable a izolované prostředí pro spuštění kontejnerizované úlohy. Z důvodu těchto atributů díky ACI vynikající platformu pro spuštění úlohy sestavení volaných ve velkém měřítku. Tento článek vás provede nasazení a použití volaných server, který je předem nakonfigurován s ACI jako cíl sestavení.

Další informace o kontejneru instance Azure, najdete v části [o instancí kontejnerů Azure][about-aci].

## <a name="deploy-a-jenkins-server"></a>Nasazení serveru volaných

1. Na portálu Azure vyberte **vytvořit prostředek** a vyhledejte **volaných**. Vyberte nabídku volaných se vydavatele **Microsoft**a potom vyberte **vytvořit**.

2. Zadejte následující informace **Základy** tvoří a potom vyberte **OK**.

   - **Název**: Zadejte název pro nasazení volaných.
   - **Uživatelské jméno**: Zadejte název pro uživatele správce volaných virtuálního počítače.
   - **Typ ověřování**: doporučujeme veřejný klíč SSH pro ověřování. Pokud vyberete tuto možnost, vložte veřejný klíč SSH, který se má použít pro přihlášení k virtuálnímu počítači volaných.
   - **Předplatné:** Vyberte předplatné Azure.
   - **Skupina prostředků:** Vytvořte skupinu prostředků nebo vyberte stávající.
   - **Umístění**: Vyberte umístění pro volaných server.

   ![Základní nastavení pro nasazení portálu volaných](./media/container-instances-jenkins/jenkins-portal-01.png)

3. Na **další nastavení** formuláři, dokončete následující položky:

   - **Velikost**: Vyberte možnost odpovídající velikosti vašeho volaných virtuálního počítače.
   - **Typ disku virtuálního počítače**: Zadejte buď **HDD** (jednotku pevného disku) nebo **SSD** (jednotka SSD) pro server volaných.
   - **Virtuální síť**: vyberte šipku, pokud chcete upravit výchozí nastavení.
   - **Podsítě**: vyberte šipku, zkontrolujte zadané informace a vyberte **OK**.
   - **Veřejná IP adresa**: vyberte šipku pro vlastní pojmenujte veřejnou IP adresu, nastavte verze SKU a nastavte jako metodu přiřazení.
   - **Popisek názvu domény**: Zadejte hodnotu pro vytvoření plně kvalifikovanou adresu URL pro virtuální počítač volaných.
   - **Typ verze volaných**: typ požadovanou verzi, vyberte z možností: **LTS**, **týdně sestavení**, nebo **Azure ověřit**.

   ![Další nastavení pro nasazení portálu volaných](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Hlavní integrace služby, vyberte **Auto(MSI)** tak, aby měl [identita spravované služby Azure] [ managed-service-identity] automaticky vytvořit identitu ověřování pro volaných instance. Vyberte **ruční** k zadání přihlašovacích údajů hlavní vlastní službu.

5. Agenti cloudu nakonfigurujte Cloudová platforma pro volaných sestavení úlohy. Z důvodu tohoto článku, vyberte **ACI**. S agentem ACI cloudu Každá úloha sestavení volaných běží v instanci kontejneru.

   ![Nastavení integrace do cloudu pro nasazení portálu volaných](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Když jste hotovi s nastavení integrace, vyberte **OK**a potom vyberte **OK** znovu v souhrnu ověření. Vyberte **vytvořit** na **podmínky použití** souhrnu. Server volaných trvá několik minut pro nasazení.

## <a name="configure-jenkins"></a>Konfigurace Jenkinse

1. Na portálu Azure přejděte do skupiny prostředků volaných, vyberte virtuální počítač volaných a poznamenejte si název DNS.

   ![Název DNS v podrobnostech o volaných virtuálního počítače](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Vyhledejte název DNS virtuálního počítače volaných a zkopírujte vrácený řetězec SSH.

   ![Pokyny přihlášení volaných řetězcem SSH](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Otevřete relaci terminálu ve vývojovém systému a vložte řetězec SSH z poslední krok. Aktualizace `username` na uživatelské jméno, kterou jste zadali při nasazení serveru volaných.

4. Po relaci je připojen, spusťte následující příkaz k načtení hesla počáteční správce:

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Nechte relace SSH a tunelové propojení systémem a přejděte na http://localhost:8080 v prohlížeči. Vložte do pole heslo počáteční správce a pak vyberte **pokračovat**.

   !["Odemknout volaných" obrazovky s pole pro heslo správce](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Vyberte **nainstalovat navrhované modulů plug-in** nainstalujte všechny doporučené volaných modulů plug-in.

   ![Obrazovka "Přizpůsobit volaných" s "Instalace navrhované modulů plug-in" vybrali](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Vytvořte uživatelský účet správce. Tento účet se používá pro přihlášení k a práci s vaší instancí volaných.

   !["Vytvořit první uživatel s oprávněními správce" obrazovky s přihlašovacími údaji vyplněno](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Vyberte **uložit a dokončení**a potom vyberte **začít používat volaných** k dokončení konfigurace.

Volaných je nyní nakonfigurována a připravena k vytvoření a nasazení kódu. V tomto příkladu jednoduchou aplikaci Java slouží k předvedení volaných sestavení v instancích kontejner Azure.

## <a name="create-a-build-job"></a>Vytvořit úlohu sestavení

Při použití image kontejneru jako volaných sestavení target, budete muset zadat obrázek, který obsahuje všechny tooling potřebné pro úspěšné sestavení. Chcete-li určit bitovou kopii:

1. Vyberte **spravovat volaných** > **nakonfigurujte systém** a přejděte dolů k položce **cloudu** části. V tomto příkladu aktualizujte hodnotu Docker bitovou kopii k **microsoft nebo java na azure volaných podřízená**.

   Až budete hotoví, vyberte **Uložit** vrátit na řídicí panel volaných.

   ![Konfigurace cloudu volaných](./media/container-instances-jenkins/jenkins-aci-image.png)

2. Nyní vytvoří úlohu volaných sestavení. Vyberte **nová položka**, zadejte například název sestavení projektu **aci-java ukázku**, vyberte **volný styl projektu**a vyberte **OK**.

   ![Pole pro název sestavení úlohy, a seznam typů projektu](./media/container-instances-jenkins/jenkins-new-job.png)

3. V části **Obecné**, ujistěte se, že **omezit, kde můžete spustit tento projekt** je vybrána. Zadejte **linux** pro výraz popisku. Tato konfigurace zajišťuje, že tato úloha sestavení běží v cloudu ACI.

   !["Obecné" kartě s podrobnostmi konfigurace](./media/container-instances-jenkins/jenkins-job-01.png)

4. V části **správu zdrojového kódu**, vyberte **Git** a zadejte **https://github.com/spring-projects/spring-petclinic.git** pro adresu URL úložiště. Toto úložiště GitHub obsahuje ukázkový kód aplikace.

   !["Zdroje kódu správy" kartu s informacemi o zdrojovém kódu](./media/container-instances-jenkins/jenkins-job-02.png)

5. V části **sestavení**, vyberte **přidat krok sestavení** a vyberte **vyvolání nejvyšší úrovně cíle Maven**. Zadejte **balíček** jako cíl krok sestavení.

   !["Sestavení" karta s výběr krok sestavení](./media/container-instances-jenkins/jenkins-job-03.png)

6. Vyberte **Uložit**.

## <a name="run-the-build-job"></a>Spustit úlohu sestavení

Testovací úloha sestavení a sledovat instancí kontejnerů Azure jako platformu sestavení, spusťte ručně sestavení.

1. Vyberte **sestavení teď** při spuštění úlohy sestavení. Jak dlouho trvá několik minut na spuštění úlohy. Byste měli vidět stav, který je podobný na následujícím obrázku:

   !["Sestavení historie" informace se stavem úlohy](./media/container-instances-jenkins/jenkins-job-status.png)

2. Když úloha běží, otevřete portál Azure a podívejte se na skupině prostředků volaných. Měli byste vidět, vytvořený kontejner instance. Úloha volaných běží uvnitř této instance.

   ![Kontejner instancí ve skupině prostředků](./media/container-instances-jenkins/jenkins-aci.png)

3. Volaných běží víc úloh než nakonfigurované počet volaných vykonavatelů (výchozí 2), vytváření více instancí kontejneru.

   ![Nově vytvořený kontejner instancí](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Po dokončení všech úloh sestavení, se odeberou instancí kontejnerů.

   ![Skupina prostředků se odebrat instancí kontejnerů](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>Další postup

Další informace o volaných v Azure najdete v tématu [Azure a volaných][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md
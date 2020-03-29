---
title: Spuštění továrny bitových bitových zjištění z Azure DevOps v laboratořích Azure DevTest Labs
description: Tento článek popisuje všechny přípravy potřebné ke spuštění výroby bitových obrázků z Azure DevOps (dříve Visual Studio Team Services).
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bb67f765684c77ed5f8527226bef578e450579e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758678"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Spuštění objektu pro vytváření imagí z Azure DevOps
Tento článek popisuje všechny přípravy potřebné ke spuštění výroby bitových obrázků z Azure DevOps (dříve Visual Studio Team Services).

> [!NOTE]
> Jakýkoliv orchestrační modul bude fungovat! Azure DevOps není povinné. Továrna bitových bitových zjištění se spouštěla pomocí skriptů Azure PowerShell, takže ji lze spustit ručně pomocí Plánovače úloh systému Windows, jiných systémů CI/CD a tak dále.

## <a name="create-a-lab-for-the-image-factory"></a>Vytvoření testovacího prostředí pro továrnu na obrázky
Prvním krokem při nastavování vytváření bitových zjištění je vytvoření testovacího prostředí v laboratořích Azure DevTest Labs. Tato laboratoř je testovací prostředí továrny na obrázky, kde vytváříme virtuální počítače a ukládáme vlastní image. Tato laboratoř je považována za součást celkového procesu výroby obrázků. Jakmile vytvoříte testovací prostředí, nezapomeňte název uložit, protože ho budete později potřebovat.

## <a name="scripts-and-templates"></a>Skripty a šablony
Dalším krokem při přijímání továrny na obrázky pro váš tým je pochopit, co je k dispozici. Skripty a šablony továrny na obrázky jsou veřejně dostupné v [úložišti GitHub DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Zde je nástin kusů:

- Továrna obrázků. Je to kořenová složka.
    - Konfigurace. Vstupy do továrny na obrázky
        - Zlaté obrázky. Tato složka obsahuje soubory JSON, které představují definice vlastních bitových kopií.
        - Labs.json. Soubor, kde se týmy zaregistrují k odběru konkrétních vlastních obrázků.
- Skripty. Motor pro továrnu na image.

Články v této části obsahují další podrobnosti o těchto skriptech a šablonách.

## <a name="create-an-azure-devops-team-project"></a>Vytvoření týmového projektu Azure DevOps
Azure DevOps umožňují ukládat zdrojový kód, spouštět Azure PowerShell na jednom místě. Můžete naplánovat opakované spuštění tak, aby byly obrázky aktuální. Existují dobré zařízení pro protokolování výsledků diagnostikovat všechny problémy.  Použití Azure DevOps není požadavek, ale můžete použít libovolný svazek/modul, který se můžete připojit k Azure a můžete spustit Azure PowerShell.

Pokud máte existující účet DevOps nebo projekt, který chcete použít místo toho, tento krok přeskočte.

Pokud chcete začít, vytvořte si bezplatný účet v Azure DevOps. Navštivte https://www.visualstudio.com/ a **vyberte Možnost Začít zdarma** přímo pod **Azure DevOps** (dříve VSTS). Budete muset zvolit jedinečný název účtu a ujistěte se, že chcete spravovat kód pomocí Gitu. Po vytvoření uložte adresu URL do týmového projektu. Zde je ukázková `https://<accountname>.visualstudio.com/MyFirstProject`adresa URL: .

## <a name="check-in-the-image-factory-to-git"></a>Změna v továrně obrázků na Git
Všechny PowerShell, šablony a konfigurace pro image factory jsou umístěny ve [veřejném úložišti DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Nejrychlejší způsob, jak získat kód do nového týmového projektu, je import úložiště. To táhne v celém úložišti DevTest Labs (takže dostanete další dokumenty a ukázky).

1. Navštivte projekt Azure DevOps, který jste vytvořili v předchozím kroku (adresa URL vypadá **jako\//\<https: accountname>.visualstudio.com/MyFirstProject).**
2. Vyberte **Import ovat úložiště**.
3. Zadejte **adresu URL klonu** pro repo `https://github.com/Azure/azure-devtestlab`devTest Labs: .
4. Vyberte **Importovat**.

    ![Importovat úložiště Git](./media/set-up-devops-lab/import-git-repo.png)

Pokud se rozhodnete pouze zkontrolovat, co je potřeba (soubory továrny na obrázky), postupujte podle pokynů [zde](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) klonovat úložiště Git a tlačit pouze soubory umístěné v **adresáři skriptů / ImageFactory.**

## <a name="create-a-build-and-connect-to-azure"></a>Vytvoření sestavení a připojení k Azure
V tomto okamžiku máte zdrojové soubory uložené v úložišti Git v Azure DevOps. Teď je potřeba nastavit kanál pro spuštění Azure PowerShellu. Existuje mnoho možností, jak provést tyto kroky. V tomto článku použijete definici sestavení pro jednoduchost, ale funguje s DevOps Sestavení, DevOps verze (jeden nebo více prostředí), jiné spuštění moduly, jako je Plánovač úloh systému Windows nebo jakýkoli jiný svazek, který může spustit Azure PowerShell.

> [!NOTE]
> Jeden důležitý bod, který je třeba mít na paměti, že některé soubory prostředí PowerShell trvat dlouhou dobu ke spuštění, když existuje mnoho (10 +) vlastní obrázky k vytvoření. Zdarma hostované DevOps Build / Release agenti mají časový čas 30 min, takže nemůžete použít zdarma hostovaného agenta, jakmile začnete vytvářet mnoho obrázků. Tato výzva pro časový plán se vztahuje na jakýkoli postroj, který se rozhodnete použít, je vhodné předem ověřit, že můžete rozšířit typické časové to-outy pro dlouho běžící skripty Azure PowerShellu. V případě Azure DevOps můžete použít placené hostované agenty nebo použít vlastního agenta sestavení.

1. Chcete-li začít, vyberte **Nastavit sestavení** na domovské stránce projektu DevOps:

    ![Tlačítko Sestavení instalačního programu](./media/set-up-devops-lab/setup-build-button.png)
2. Zadejte **název** sestavení (například: Sestavení a doručení bitových kopií do testovacích laboratoří DevTest).
3. Vyberte **prázdnou** definici sestavení a vyberte **Použít** k vytvoření sestavení.
4. V této fázi můžete zvolit **Hosted** pro agenta sestavení.
5. **Uložte** definici sestavení.

    ![Definice sestavení](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Konfigurace proměnných sestavení
Chcete-li zjednodušit parametry příkazového řádku, zapouzdřte hodnoty klíče, které řídí továrnu bitové kopie, do sady proměnných sestavení. Vyberte kartu **Proměnné** a zobrazí se seznam několika výchozích proměnných. Tady je seznam proměnných, které chcete zadat do Azure DevOps:


| Název proměnné | Hodnota | Poznámky |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Skripty/ImageFactory/Konfigurace | Toto je úplná cesta v úložišti do složky **Configuration.** Pokud jste importovali celý repo výše, hodnota vlevo je správná. V opačném případě aktualizujte tak, aby přecštová na umístění konfigurace. |
| Název DevTestLab | MyImageFactory | Název testovacího prostředí v Azure DevTest Labs používá jako továrna k výrobě bitových kopií. Pokud ho nemáte, vytvořte si ho. Ujistěte se, že testovací hospodařilo ve stejném předplatném, ke kterému má koncový bod služby přístup. |
| Retence obrázků | 1 | Počet obrázků, které chcete uložit každého typu. Nastavte výchozí hodnotu na hodnotu 1. |
| Strojové heslo | ******* | Předdefinované heslo účtu správce pro virtuální počítače. Jedná se o přechodný účet, takže se ujistěte, že je zabezpečený. Vyberte malou ikonu zámku vpravo, abyste se ujistili, že se jedná o bezpečný řetězec. |
| MachineUserName | ImageFactoryUser | Předdefinované uživatelské jméno účtu správce pro virtuální počítače. Toto je přechodný účet. |
| StandardTimeoutMinutes | 30 | Časový čas, který bychom měli čekat na běžné operace Azure. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | ID předplatného, kde existuje testovací prostředí a ke kterému má koncový bod služby přístup. |
| Velikost virtuálního počítače | Standard_A3 | Velikost virtuálního počítače, který se má použít pro krok **Vytvořit.** Vytvořené virtuální chody jsou přechodné. Velikost musí být ta, která je [povolena pro testovací prostředí](devtest-lab-set-lab-policy.md). Zkontrolujte, zda je k dispozici dostatečná [kvóta předplacených jader](../azure-resource-manager/management/azure-subscription-service-limits.md).

![Vytváření proměnných](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Připojení k Azure
Dalším krokem je nastavení instančního objektu. Toto je identita ve službě Azure Active Directory, která umožňuje agentovi sestavení DevOps pracovat v Azure jménem uživatele. Chcete-li ji nastavit, začněte přidáním prvního kroku sestavení Azure PowerShellu.

1. Vyberte **Přidat úkol**.
2. Vyhledejte **Azure PowerShell**.
3. Jakmile ji najdete, vyberte **Přidat** a přidejte úkol do sestavení. Když toto uděláte, zobrazí se úkol na levé straně jako přidán.

![Nastavení kroku PowerShellu](./media/set-up-devops-lab/set-up-powershell-step.png)

Nejrychlejší způsob, jak nastavit instanční objekt, je nechat Azure DevOps, aby to udělalza nás.

1. Vyberte **úkol,** který jste právě přidali.
2. Pro **typ připojení Azure**zvolte Azure Resource **Manager**.
3. Chcete-li nastavit instanční objekt, vyberte odkaz **Spravovat.**

Další informace najdete v tomto [příspěvku na blogu](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Když vyberete odkaz **Spravovat,** přistanete na správném místě v DevOps (druhý snímek obrazovky v příspěvku blogu) a nastavíte připojení k Azure. Při nastavování této možnosti nezapomeňte zvolit **koncový bod služby Azure Resource Manager.**

## <a name="complete-the-build-task"></a>Dokončení úlohy sestavení
Pokud vyberete úlohu sestavení, zobrazí se všechny podrobnosti v pravém podokně, které by měly být vyplněny.

1. Nejprve pojmenujte úlohu sestavení: **Vytvořit virtuální počítače**.
2. Výběrem nástroje **Azure Resource Manager** zvolte **instanční objekt,** který jste vytvořili.
3. Zvolte **koncový bod služby**.
4. V **případě Cesty skriptu**vyberte **... (tři tečky)** vpravo.
5. Přejděte na skript **MakeGoldenImageVMs.ps1.**
6. Parametry skriptu by měly vypadat takto:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Dokončení definice sestavení](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Zařazování sestavení do fronty
Ověříme, že máte vše správně nastaveno tak, že zařazujete nové sestavení do fronty. Když je sestavení spuštěné, přepněte na [portál Azure](https://portal.azure.com) a vyberte na **všech virtuálních počítačích** v testovacím prostředí továrny na bitové kopie, abyste potvrdili, že vše funguje správně. Měli byste vidět tři virtuální počítače vytvořit v testovacím prostředí.

![Virtuální virtuální chod v testovacím prostředí](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Další kroky
První krok při nastavování výroby bitových zjištění na základě Azure DevTest Labs je dokončen. V dalším článku v sérii získáte tyto virtuální chody zobecněné a uložené do vlastních obrázků. Pak je distribuujete do všech ostatních laboratoří. Podívejte se na další článek v sérii: [Uložte vlastní obrázky a distribuovat do více laboratoří](image-factory-save-distribute-custom-images.md).

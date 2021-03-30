---
title: Spuštění objektu pro vytváření imagí z Azure DevOps v Azure DevTest Labs
description: Tento článek pojednává o všech přípravcích potřebných ke spuštění objektu pro vytváření imagí z Azure DevOps (dříve Visual Studio Team Services).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fa7050bae1ff8681e04b6ab38220be9eaf38a64a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476134"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Spuštění objektu pro vytváření imagí z Azure DevOps
Tento článek pojednává o všech přípravcích potřebných ke spuštění objektu pro vytváření imagí z Azure DevOps (dříve Visual Studio Team Services).

> [!NOTE]
> Všechny moduly orchestrace budou fungovat! Azure DevOps není povinný. Objekt pro vytváření imagí se spouští pomocí Azure PowerShell skriptů, takže ho můžete spustit ručně, a to pomocí Plánovač úloh Windows, dalších systémů CI/CD a tak dále.

## <a name="create-a-lab-for-the-image-factory"></a>Vytvoření testovacího prostředí pro objekt pro vytváření imagí
Prvním krokem při nastavení továrny imagí je vytvoření testovacího prostředí v Azure DevTest Labs. Toto testovací prostředí je prostředí pro vytváření imagí, kde vytvoříme virtuální počítače a uložíme vlastní image. Tato laboratoř se považuje za součást celkového procesu vytváření imagí. Po vytvoření testovacího prostředí nezapomeňte název uložit, protože ho budete potřebovat později.

## <a name="scripts-and-templates"></a>Skripty a šablony
Dalším krokem při přijímání továrny imagí pro váš tým je pochopit, co je k dispozici. Skripty a šablony pro vytváření imagí jsou veřejně dostupné v [úložišti GitHubu pro DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Tady je osnova těchto částí:

- Objekt pro vytváření imagí Jedná se o kořenovou složku.
    - Rozšířeného. Vstupy do objektu pro vytváření imagí
        - GoldenImages. Tato složka obsahuje soubory JSON, které reprezentují definice vlastních imagí.
        - Labs.js. Soubor, ve kterém se týmy registrují pro příjem konkrétních vlastních imagí.
- Znakové. Modul pro objekt pro vytváření imagí.

Články v této části poskytují další podrobnosti o těchto skriptech a šablonách.

## <a name="create-an-azure-devops-team-project"></a>Vytvoření týmového projektu Azure DevOps
Azure DevOps vám umožní uložit zdrojový kód, spustit Azure PowerShell na jednom místě. Můžete naplánovat opakovaná spuštění, aby se image udržovaly v aktuálním stavu. Existují vhodné možnosti pro protokolování výsledků za účelem diagnostiky problémů.  Používání Azure DevOps se ale nepožaduje, ale můžete použít libovolný modul, který se může připojit k Azure a může spustit Azure PowerShell.

Pokud máte existující účet DevOps nebo projekt, který byste chtěli použít, tento krok přeskočte.

Pokud chcete začít, vytvořte si bezplatný účet ve službě Azure DevOps. Přejděte na adresu https://www.visualstudio.com/ a v části **Azure DevOps** (dříve VSTS) vyberte začít hned **zdarma** . Budete muset zvolit jedinečný název účtu a ujistit se, že se rozhodnete spravovat kód pomocí Gitu. Po vytvoření uložte adresu URL do týmového projektu. Tady je ukázková adresa URL: `https://<accountname>.visualstudio.com/MyFirstProject` .

## <a name="check-in-the-image-factory-to-git"></a>Vrácení továrny imagí do Gitu
Všechny prostředí PowerShell, šablony a konfigurace pro objekt pro vytváření imagí se nacházejí v [úložišti GitHub veřejné DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Nejrychlejší způsob, jak získat kód do nového týmového projektu, je importovat úložiště. Tato operace se vyžádá v celém úložišti DevTest Labs (takže získáte další dokumenty a ukázky).

1. Přejděte na projekt Azure DevOps, který jste vytvořili v předchozím kroku (adresa URL vypadá jako **https: \/ / \<accountname> . VisualStudio.com/MyFirstProject**).
2. Vyberte **importovat úložiště**.
3. Zadejte **adresu URL klonu** pro úložiště DevTest Labs: `https://github.com/Azure/azure-devtestlab` .
4. Vyberte **Importovat**.

    ![Importovat úložiště Git](./media/set-up-devops-lab/import-git-repo.png)

Pokud se rozhodnete pouze přesně zjistit, co je potřeba (soubory pro vytváření imagí), naklonujte [](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) úložiště Git a nahrajte jenom soubory umístěné v adresáři **Scripts/ImageFactory** .

## <a name="create-a-build-and-connect-to-azure"></a>Vytvoření sestavení a připojení k Azure
V tomto okamžiku máte zdrojové soubory uložené v úložišti Git ve službě Azure DevOps. Teď je potřeba nastavit kanál pro spuštění Azure PowerShell. K dispozici je spousta možností, jak provést tyto kroky. V tomto článku použijete definici sestavení pro jednoduchost, ale spolupracuje s DevOps Build, DevOps vydáním (jedním nebo několika prostředími), jinými spouštěcími moduly, jako je Windows Plánovač úloh nebo jakýmkoli jiným způsobem, který může Azure PowerShell spustit.

> [!NOTE]
> Důležité je, abyste měli na paměti, že spuštění některých souborů PowerShell trvá dlouhou dobu, když máte spoustu (10) vlastních imagí, které se mají vytvořit. Bezplatná hostované DevOps sestavení/vydání mají časový limit 30 minut, takže nemůžete používat bezplatného hostovaného agenta, když začnete sestavovat spoustu imagí. Tato výzva k vypršení platnosti se vztahuje na libovolný svazek, který se rozhodnete použít, je vhodné ověřit, že můžete prodloužit obvyklé časové limity pro dlouhé běžící Azure PowerShell skripty. V případě Azure DevOps můžete buď použít placené hostované agenty, nebo použít vlastního agenta sestavení.

1. Začněte výběrem **nastavit sestavení** na domovské stránce projektu DevOps:

    ![Tlačítko pro sestavení instalace](./media/set-up-devops-lab/setup-build-button.png)
2. Zadejte **název** sestavení (například: Sestavte a doručovat images do DevTest Labs).
3. Vyberte **prázdnou** definici sestavení a vyberte **použít** pro vytvoření sestavení.
4. V této fázi můžete zvolit možnost **hostované** pro agenta sestavení.
5. **Uložte** definici sestavení.

    ![Definice sestavení](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Konfigurovat proměnné sestavení
Chcete-li zjednodušit parametry příkazového řádku, zapouzdřujte klíčové hodnoty, které řídí objekt pro vytváření imagí, do sady proměnných sestavení. Vyberte kartu **proměnné** a zobrazí se seznam několika výchozích proměnných. Tady je seznam proměnných, které se mají zadat do Azure DevOps:


| Název proměnné | Hodnota | Poznámky |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | Toto je úplná cesta v úložišti ke složce **Konfigurace** . Pokud jste importovali celé úložiště výše, hodnota vlevo je správná. Jinak aktualizujte tak, aby odkazovaly na umístění konfigurace. |
| DevTestLabName | MyImageFactory | Název testovacího prostředí v Azure DevTest Labs použitý jako továrna k vytváření imagí. Pokud ho ještě nemáte, vytvořte ho. Ujistěte se, že je testovací prostředí ve stejném předplatném, ke kterému má koncový bod služby přístup. |
| ImageRetention | 1 | Počet imagí, které chcete uložit u každého typu. Nastavte výchozí hodnotu na 1. |
| MachinePassword | ******* | Předdefinované heslo účtu správce pro virtuální počítače. Toto je přechodný účet, proto se ujistěte, že je zabezpečený. Vyberte ikonu trochu zámku na pravé straně, abyste zajistili, že se jedná o zabezpečený řetězec. |
| MachineUserName | ImageFactoryUser | Předdefinované uživatelské jméno účtu správce pro virtuální počítače. Toto je přechodný účet. |
| StandardTimeoutMinutes | 30 | Časový limit, který by měl počkat na pravidelné operace Azure. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | ID předplatného, ve kterém testovací prostředí existuje a ke kterému má koncový bod služby přístup. |
| VMSize | Standard_A3 | Velikost virtuálního počítače, který se má použít pro krok **Vytvoření** Vytvořené virtuální počítače jsou přechodné. Velikost musí být ta, která je [povolená pro testovací prostředí](devtest-lab-set-lab-policy.md). Potvrďte, že existuje dostatečná [kvóta jader předplatného](../azure-resource-manager/management/azure-subscription-service-limits.md).

![Proměnné sestavení](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Připojení k Azure
Dalším krokem je nastavení instančního objektu. Jedná se o identitu v Azure Active Directory, která umožňuje agentovi sestavení DevOps provozovat v Azure jménem uživatele. Pokud ho chcete nastavit, Začněte přidáním prvního Azure PowerShell kroku sestavení.

1. Vyberte **Přidat úlohu**.
2. Vyhledejte **Azure PowerShell**.
3. Jakmile ho najdete, vyberte **Přidat** a přidejte úkol do sestavení. Když to uděláte, zobrazí se na levé straně úkol, který se zobrazí jako přidaný.

![Nastavení kroku prostředí PowerShell](./media/set-up-devops-lab/set-up-powershell-step.png)

Nejrychlejší způsob, jak nastavit instanční objekt, je umožnit službě Azure DevOps to pro nás.

1. Vyberte **úlohu** , kterou jste právě přidali.
2. Jako **Typ připojení Azure** vyberte **Azure Resource Manager**.
3. Pro nastavení instančního objektu vyberte odkaz pro **správu** .

Další informace najdete v tomto [příspěvku na blogu](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Když vyberete odkaz pro **správu** , budete mít na správném místě v DevOps (druhý snímek obrazovky v příspěvku blogu) a nastavíte připojení k Azure. Nezapomeňte při nastavování zvolit **Azure Resource Manager koncový bod služby** .

## <a name="complete-the-build-task"></a>Dokončení úlohy sestavení
Pokud vyberete úlohu sestavení, zobrazí se všechny podrobnosti v pravém podokně, které by měly být vyplněny.

1. Nejprve pojmenujte úlohu sestavení: **vytvořit Virtual Machines**.
2. Vyberte **objekt služby** , který jste vytvořili, volbou **Azure Resource Manager**
3. Vyberte **koncový bod služby**.
4. Jako **cestu ke skriptu** vyberte **... (tři tečky)** napravo.
5. Přejděte na **MakeGoldenImageVMs.ps1** skript.
6. Parametry skriptu by měly vypadat takto: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Dokončete definici sestavení.](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Zařazení sestavení do fronty
Pojďme si ověřit, jestli máte všechno správně nastavené, a to zařazením nového buildu do fronty. Když je sestavení spuštěné, přepněte na [Azure Portal](https://portal.azure.com) a v testovacím prostředí pro vytváření imagí vyberte **všechny Virtual Machines** , abyste ověřili, že vše funguje správně. V testovacím prostředí byste měli vidět tři virtuální počítače.

![Virtuální počítače v testovacím prostředí](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Další kroky
První krok nastavení objektu pro vytváření imagí založený na Azure DevTest Labs je dokončený. V dalším článku v řadě získáte tyto virtuální počítače generalizované a uložené do vlastních imagí. Pak je budete distribuovat do všech ostatních cvičení. Podívejte se na další článek v řadě: [uložení vlastních imagí a distribuce do více cvičení](image-factory-save-distribute-custom-images.md).

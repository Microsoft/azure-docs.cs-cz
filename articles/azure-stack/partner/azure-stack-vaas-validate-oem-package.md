---
title: Ověření balíčky výrobce OEM (OEM) v Azure stacku ověření jako služba | Dokumentace Microsoftu
description: Zjistěte, jak ověřit výrobce OEM (OEM) balíčky s ověřováním jako služba.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c2b0343ff472fe380750152712ca88d9ebb404e2
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57782781"
---
# <a name="validate-oem-packages"></a>Ověření balíčky výrobce OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Nový balíček pro výrobce OEM můžete otestovat, pokud došlo ke změně firmware a ovladače pro ověření dokončeného řešení. Pokud váš balíček uplynutí testu, je podepsán společností Microsoft. Váš test musí obsahovat aktualizovaný balíček rozšíření výrobcem OEM s ovladače a firmware, které prošly logo systému Windows Server a počítačů s testy.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Před odesláním nebo odeslání balíčků, zkontrolujte [vytvoření balíčku výrobce OEM](azure-stack-vaas-create-oem-package.md) formátu očekávaném balíčku a obsah.

## <a name="managing-packages-for-validation"></a>Správa balíčků pro ověření

Při použití **ověřování balíčku** pracovního postupu se ověřit balíček, budete muset zadat adresu URL **Azure Storage blob**. Tento objekt blob je, že test podepsaný balíček výrobce OEM, který se nainstaluje jako součást procesu aktualizace. Vytvoření objektů blob v účtu úložiště Azure, který jste vytvořili během instalace (viz [nastavení ověření jako prostředky služby](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Předpoklad: Zřízení kontejner úložiště.

Vytvoření kontejneru v účtu úložiště pro objekty BLOB balíčku. Tento kontejner je možné pro všechno, co váš balíček ověřování je spuštěno.

1. V [webu Azure portal](https://portal.azure.com), přejděte na účet úložiště vytvořený v [nastavení ověření jako prostředky služby](azure-stack-vaas-set-up-resources.md).

2. V levém okně v části **služby Blob Service**vyberte **kontejnery**.

3. Vyberte **+ kontejner** z řádku nabídek.
    1. Zadejte název kontejneru, například `vaaspackages`.
    1. Vyberte úroveň přístupu požadované pro neověřené klienty, jako je například VaaS. Podrobnosti o tom, jak udělit přístup VaaS na balíčky v každém scénáři najdete v tématu [zpracování úroveň přístupu kontejneru](#handling-container-access-level).

### <a name="upload-package-to-storage-account"></a>Nahrání balíčku do účtu úložiště

1. Připravte balíček, který chcete ověřit. Jde `.zip` souboru, jehož obsah musí odpovídat struktura je popsáno v [vytvoření balíčku výrobce OEM](azure-stack-vaas-create-oem-package.md).

    > [!NOTE]
    > Ujistěte se prosím, `.zip` obsah jsou umístěné v kořenovém adresáři `.zip` soubor. V balíčku, měla by existovat žádné podsložky.

1. V [webu Azure portal](https://portal.azure.com), vyberte kontejner balíčku a balíček uložit tak, že vyberete na **nahrát** v panelu nabídek.

1. Vyberte balíček `.zip` soubor k nahrání. Ponechte výchozí hodnoty pro **typ blobu** (to znamená **objektů Blob bloku**) a **velikost bloku**.

### <a name="generate-package-blob-url-for-vaas"></a>Generování adresy URL objektu blob balíčku pro VaaS

Při vytváření **ověřování balíčku** pracovního postupu portálu VaaS, budete muset zadat adresu URL do služby Azure Storage blob obsahující balíček. Některé *interaktivní* testy, včetně **měsíční aktualizace ověření AzureStack** a **ověřování balíčků rozšíření OEM**, vyžadují také adresu URL pro objekty BLOB balíčku.

#### <a name="handling-container-access-level"></a>Úroveň přístupu kontejneru zpracování

Úroveň přístupu minimální vyžadovanou VaaS závisí na Určuje, zda vytváříte pracovní postup ověření balíčku nebo plánování *interaktivní* testování.

V případě třídy **privátní** a **Blob** úrovní, přístupu dočasně musí udělit přístup k objektu blob balíčku tak, že udělíte VaaS [sdílený přístupový podpis](https://docs.microsoft.com/en-us/azure/storage/common/storage-dotnet-shared-access-signature-part-1?) (SAS). **Kontejneru** úroveň přístupu nevyžaduje k vygenerování adres URL SAS, ale umožňuje neověřený přístup ke kontejneru a jeho objekty BLOB.

|Úroveň přístupu | Požadavek pracovního postupu | Požadavek testu |
|---|---------|---------|
|Private | Vygenerovat adresu SAS URL na objekt blob balíčku ([možnost 1](#option-1-generate-a-blob-sas-url)). | Generování adresy URL SAS na úrovni účtu a ručně přidejte název objektu blob balíčku ([možnost 2](#option-2-construct-a-container-sas-url)). |
|Objekt blob | Zadejte vlastnost adresa URL objektu blob ([možnost 3](#option-3-grant-public-read-access)). | Generování adresy URL SAS na úrovni účtu a ručně přidejte název objektu blob balíčku ([možnost 2](#option-2-construct-a-container-sas-url)). |
|Kontejner | Zadejte vlastnost adresa URL objektu blob ([možnost 3](#option-3-grant-public-read-access)). | Zadejte vlastnost adresa URL objektu blob ([možnost 3](#option-3-grant-public-read-access)).

Možnosti pro udělení přístupu ke své balíčky jsou seřazeny od nejnižší přístup po největší přístup.

#### <a name="option-1-generate-a-blob-sas-url"></a>Option 1: Vygenerovat adresu SAS URL objektu blob

Tuto možnost použijte, pokud je úroveň přístupu kontejneru úložiště nastavená **privátní**, kde kontejneru neumožňuje veřejné oprávnění ke čtení ke kontejneru a jeho objekty BLOB.

> [!NOTE]
> Tato metoda nebude fungovat pro *interaktivní* testy. Zobrazit [možnost 2: Vytvoření kontejneru SAS URL](#option-2-construct-a-container-sas-url).

1. V [webu Azure portal](https://portal.azure.com/), přejdete do účtu úložiště a pak na ZIP, který obsahuje váš balíček.

2. Vyberte **generovat SAS** v místní nabídce.

3. Vyberte **čtení** z **oprávnění**.

4. Nastavte **počáteční čas** na aktuální čas a **čas ukončení** aspoň 48 hodin od **počáteční čas**. Pokud budete vytvářet další pracovní postupy pomocí stejného balíčku, zvažte zvýšení **čas ukončení** délky testování.

5. Vyberte **vygenerujte token SAS objektů blob a adresa URL**.

Použití **SAS URL objektu Blob** při poskytování balíček blob adresy URL na portálu.

#### <a name="option-2-construct-a-container-sas-url"></a>Option 2: Vytvořit kontejner adresy URL SAS

Tuto možnost použijte, pokud je úroveň přístupu kontejneru úložiště nastavená **privátní** a budete muset zadat adresu URL balíčku objektů blob do *interaktivní* testování. Tuto adresu URL můžete použít také na úrovni pracovního postupu.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Vyberte **Blob** z **možnosti povolené služby**. Zrušit všechny zbývající možnosti.

1. Vyberte **kontejneru** a **objekt** z **povolené typy prostředků**.

1. Vyberte **čtení** a **seznamu** z **povolené oprávnění**. Zrušit všechny zbývající možnosti.

1. Vyberte **počáteční čas** jako aktuální čas a **čas ukončení** k aspoň 14 dní od **počáteční čas**. Pokud budete používat jiné testy pomocí stejného balíčku, zvažte zvýšení **čas ukončení** délky testování. Všechny testy naplánovat prostřednictvím VaaS po **čas ukončení** nové přidružení zabezpečení a navrácení služeb po bude muset vygenerovat.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    Formát by měl vypadat takto: `https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. Upravte vygenerovaný adres URL SAS kontejneru balíčku zahrnout `{containername}`a název objektu blob služby balíček `{mypackage.zip}`, následujícím způsobem:  `https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    Používejte tuto hodnotu poskytuje balíček blob adresy URL na portálu.

#### <a name="option-3-grant-public-read-access"></a>Možnost 3: Udělte veřejné oprávnění ke čtení

Tuto možnost použijte, pokud je to přijatelné klientům neověřený přístup k jednotlivých objektů BLOB, nebo v případě třídy *interaktivní* testuje kontejneru.

> [!CAUTION]
> Tato možnost otevře vaše objektů blob pro anonymní přístup jen pro čtení.

1. Nastavit úroveň přístupu kontejneru balíčku má **Blob** nebo **kontejneru** podle pokynů v části [anonymním uživatelům uděluje oprávnění ke kontejnerům a objektům blob](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

    > [!NOTE]
    > Pokud zadáváte adresu URL balíčku *interaktivní* test, je nutné udělit **úplné veřejné oprávnění ke čtení** ke kontejneru a pokračujte v testování.

1. V kontejneru balíčku vyberte objekt blob balíček a otevřete tak podokno vlastností.

1. Kopírovat **URL**. Používejte tuto hodnotu poskytuje balíček blob adresy URL na portálu.

## <a name="create-a-package-validation-workflow"></a>Vytvořit pracovní postup ověření balíčku

1. Přihlaste se k [VaaS portál](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Vyberte **Start** na **ověřování balíčku** dlaždici.

    ![Balíček ověření pracovního postupu dlaždice](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Zadejte služby Azure Storage blob URL testu podepsané balíčku výrobce OEM vyžadující podpis od společnosti Microsoft. Pokyny najdete v tématu [generování adresy URL objektu blob balíčku pro VaaS](#generate-package-blob-url-for-vaas).

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Po vytvoření pracovního postupu není možné měnit parametry prostředí.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Budete přesměrováni na stránku Souhrn testů.

## <a name="required-tests"></a>Požadované testy

Následující testy jsou požadovány pro ověřování balíčku výrobce OEM:

- Ověřování balíčků rozšíření výrobce OEM
- Simulace modulu cloudu

## <a name="run-package-validation-tests"></a>Spustit testy pro ověření balíčku

1. V **ověřování balíčku testuje SOUHRN** stránky, se spustí podmnožinu uvedené testy přísluší vašemu scénáři.

    V pracovních postupech ověřování **plánování** testu používá společné parametry úrovni pracovního postupu, které jste zadali při vytváření pracovního postupu (viz [společných parametrů pracovních postupů pro Azure Stack ověření jako služba](azure-stack-vaas-parameters.md)). Pokud některý z hodnoty parametrů testu stane neplatným, je nutné resupply podle pokynů v tématu [upravit parametry pracovního postupu](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

    > [!NOTE]
    > Při plánování ověřovací test existující instance vytvoří novou instanci namísto původní instanci na portálu. Protokoly pro původní instanci se zachovají, ale nejsou přístupné z portálu.  
    > Po úspěšném dokončení testu **plán** akce bude zakázáno.

2. Vyberte agenta, který se spustí test. Další informace o přidávání místní testovací agenti spuštění, naleznete v tématu [nasazení místní agent](azure-stack-vaas-local-agent.md).

3. K dokončení ověřování balíčků rozšíření výrobce OEM, zvolit **plán** z místní nabídky otevřete příkazový řádek pro plánování testovací instance.

4. Zkontrolujte parametry testu a pak vyberte **odeslat** naplánování ověřování balíčků rozšíření OEM pro spuštění.

    Ověřování balíčků rozšíření OEM se dělí do dvou ruční kroky: Aktualizace služby Azure Stack a aktualizace pro výrobce OEM.

    1. **Vyberte** "Spustit" v uživatelském rozhraní pro spuštění precheck skriptu. Toto je automatické testování, který trvá asi 5 minut na dokončení a nevyžaduje zásah uživatele.

    1. Po dokončení skriptu precheck provedení ručního kroku: **nainstalovat** nejnovější dostupné služby Azure Stack aktualizace pomocí portálu Azure Stack.

    1. **Spustit** AzureStack Test na razítko. Pokud dojde k selhání, nepokračujte s testováním a kontakt [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

        Informace o tom, jak spustit příkaz Test-AzureStack najdete v tématu [stavu systému ověřit Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test).

    1. **Vyberte** tlačítko Další"pro spuštění postcheck skriptu. Toto je automatické testování a označuje konec procesu aktualizace služby Azure Stack.

    1. **Vyberte** "Spustit" ke spuštění precheck skript pro aktualizaci výrobce OEM.

    1. Po dokončení předběžná kontrola provedení ručního kroku: **nainstalovat** rozšíření balíčku výrobce OEM prostřednictvím portálu.

    1. **Spustit** AzureStack Test na razítko.

        > [!NOTE]
        > Stejně jako dříve nepokračovat s testováním a kontaktujte [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) Pokud selže. Tento krok je kritický, protože jste se uloží opětovné nasazení.

    1. **Vyberte** tlačítko Další"pro spuštění postcheck skriptu. To označuje konec krok aktualizace výrobce OEM.

    1. Všechny zbývající dotazy na konci testu a **vyberte** "Odeslat".

    1. To označuje konec interaktivního testu.

5. Zkontrolujte výsledky pro výrobce OEM ověřování balíčků rozšíření. Jakmile test proběhl úspěšně, naplánujte cloudu simulace modul pro spuštění.

K odeslání žádosti o podepsání balíčku, odeslat [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) název řešení a název balíčku ověření spojené s tímto spuštěný.

## <a name="next-steps"></a>Další postup

- [Monitorování a správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)

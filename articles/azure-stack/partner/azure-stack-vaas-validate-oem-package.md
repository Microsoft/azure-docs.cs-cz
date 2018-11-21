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
ms.date: 11/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 8268a6b04d7ddbb35821999142d3a33bdd2bedcc
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52261798"
---
# <a name="validate-oem-packages"></a>Ověření balíčky výrobce OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Nový balíček pro výrobce OEM můžete otestovat, pokud došlo ke změně firmware a ovladače pro ověření dokončeného řešení. Pokud váš balíček uplynutí testu, je podepsán společností Microsoft. Váš test musí obsahovat aktualizovaný balíček rozšíření výrobcem OEM s ovladače a firmware, které prošly logo systému Windows Server a počítačů s testy.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Před odesláním nebo odeslání balíčků, zkontrolujte [vytvoření balíčku výrobce OEM](azure-stack-vaas-create-oem-package.md) formátu očekávaném balíčku a obsah.

## <a name="managing-packages-for-validation"></a>Správa balíčků pro ověření

Při použití **ověřování balíčku** pracovního postupu se ověřit balíček, budete muset zadat adresu URL **Azure Storage blob**. Tento objekt blob je balíčku výrobce OEM, která byla nainstalována na řešení v době nasazení. Vytvoření objektů blob v účtu úložiště Azure, který jste vytvořili během instalace (viz [nastavení ověření jako prostředky služby](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Předpoklad: Zřízení kontejner úložiště.

Vytvoření kontejneru v účtu úložiště pro objekty BLOB balíčku. Tento kontejner je možné pro všechno, co váš balíček ověřování je spuštěno.

1. V [webu Azure Portal](https://portal.azure.com), přejděte na účet úložiště vytvořený v [nastavení ověření jako prostředky služby](azure-stack-vaas-set-up-resources.md).
2. V levém okně v části **služby Blob Service**, vyberte na **kontejnery**.
3. Vyberte **+ kontejner** v nabídce řádku a zadejte název kontejneru, například `vaaspackages`.

### <a name="upload-package-to-storage-account"></a>Nahrání balíčku do účtu úložiště

1. Připravte balíček, který chcete ověřit. Pokud váš balíček obsahuje více souborů, je do komprimovat `.zip` souboru.
2. V [webu Azure Portal](https://portal.azure.com), vyberte kontejner balíčku a balíček uložit tak, že vyberete na **nahrát** v panelu nabídek.
3. Vyberte balíček `.zip` soubor k nahrání. Ponechte výchozí hodnoty pro **typ blobu** (například **objektů Blob bloku**) a **velikost bloku**.

> [!NOTE]
> Ujistěte se prosím, `.zip` obsah jsou umístěné v kořenovém adresáři `.zip` soubor. V balíčku, měla by existovat žádné podsložky.

### <a name="generate-package-blob-url-for-vaas"></a>Generování adresy URL objektu blob balíčku pro VaaS

Při vytváření **ověřování balíčku** pracovního postupu portálu VaaS, budete muset zadat adresu URL do služby Azure Storage blob obsahující balíček.

#### <a name="option-1-generating-an-account-sas-url"></a>Možnost 1: Generování SAS URL účtu

1. V [webu Azure portal](https://portal.azure.com/), přejdete do účtu úložiště a pak na ZIP, který obsahuje váš balíček

2. Vyberte **generovat SAS** v místní nabídce

3. Vyberte **čtení** z **oprávnění**

4. Nastavte **počáteční čas** na aktuální čas a **čas ukončení** aspoň 48 hodin od **počáteční čas**. Pokud budete používat jiné testy pomocí stejného balíčku, zvažte zvýšení **čas ukončení** délky testování. Všechny testy naplánovat prostřednictvím VaaS po **čas ukončení** nové přidružení zabezpečení a navrácení služeb po bude muset vygenerovat.

5. Vyberte **vygenerujte token SAS objektů blob a adresy URL**

Použití **SAS URL objektu Blob** při spouštění nového **ověřování balíčku** pracovního postupu portálu VaaS.

#### <a name="option-2-using-public-read-container"></a>Možnost 2: Použití veřejného kontejneru pro čtení

> [!CAUTION]
> Tato možnost otevře se váš kontejner pro anonymní přístup jen pro čtení.

1. Udělení **veřejný přístup pro objekty BLOB pouze pro čtení** ke kontejneru balíček podle pokynů v části [anonymním uživatelům uděluje oprávnění ke kontejnerům a objektům blob](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

2. V kontejneru balíčku vyberte a otevřete tak podokno vlastností na balíček objektů blob v kontejneru.

3. Kopírovat **URL**. Použijte tuto hodnotu při spuštění nového **ověřování balíčku** pracovního postupu portálu VaaS.

## <a name="apply-monthly-update"></a>Měsíční aktualizace

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-package-validation-workflow"></a>Vytvořit pracovní postup ověření balíčku

1. Přihlaste se k [VaaS portál](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Vyberte **Start** na **ověřování balíčku** dlaždici.

    ![Balíček ověření pracovního postupu dlaždice](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Zadejte adresu URL služby Azure Storage blob do balíčku výrobce OEM, která byla nainstalována na řešení v době nasazení. Pokyny najdete v tématu [generování adresy URL objektu blob balíčku pro VaaS](#generate-package-blob-url-for-vaas).

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Po vytvoření pracovního postupu není možné měnit parametry prostředí.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Budete přesměrováni na stránku Souhrn testů.

## <a name="run-package-validation-tests"></a>Spustit testy pro ověření balíčku

V **balíček ověřovacími testy, které jsou souhrnné** stránky, zobrazí se seznam testů, které jsou potřebné k dokončení ověření. Spustit testy v tomto pracovním postupu přibližně 24 hodin.

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

Název řešení VaaS a ověřování balíčku pro odeslání, když všechny testy byly úspěšně dokončeny, [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) na žádost o podepsání balíčku.

## <a name="next-steps"></a>Další postup

- [Monitorování a správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)
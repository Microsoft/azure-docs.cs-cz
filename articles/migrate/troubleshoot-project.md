---
title: Řešení problémů s projekty Azure Migrate
description: Pomáhá řešit problémy s vytvářením a správou projektů Migrace Azure.
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: 3b8c2f6ec33965317d2aaa23a36b6becff11a54a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75725725"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Řešení problémů s projekty Azure Migrate

Tento článek vám pomůže vyřešit problémy při vytváření a správě projektů [migrace Azure.](migrate-services-overview.md)

## <a name="how-to-add-new-project"></a>Jak přidat nový projekt?

Můžete mít více projektů Migrace Azure v předplatném. [Přečtěte si, jak](how-to-add-tool-first-time.md) vytvořit projekt poprvé, nebo [přidat další](create-manage-projects.md#create-additional-projects) projekty.

## <a name="what-azure-permissions-are-needed"></a>Jaká oprávnění Azure jsou potřeba?

K vytvoření projektu Migrace Azure potřebujete oprávnění přispěvatele nebo vlastníka v předplatném.

## <a name="cant-find-a-project"></a>Nelze najít projekt

Nalezení existujícího projektu Migrace Azure závisí na tom, jestli používáte aktuální nebo starou verzi Azure Migrate. [Postupujte](create-manage-projects.md#find-a-project)podle .


## <a name="cant-find-a-geography"></a>Nelze najít geografii

Projekt Migrace Azure můžete vytvořit v [podporovaných zeměpisných oblastech](migrate-support-matrix.md#supported-geographies). Všimněte si, že geografie projektu se používá k ukládání metadat zjištěného počítače. Počítače můžete vyhodnotit nebo migrovat i v jiných umístěních.

## <a name="what-are-vm-limits"></a>Co jsou limity virtuálních mís?

Můžete vyhodnotit až 35 000 virtuálních měn VMware nebo až 35 000 virtuálních virtuálních<-v. Hyper-V v jednom projektu. Projekt může zahrnovat virtuální zařízení VMware i virtuální zařízení Hyper-V až do limitů hodnocení.

## <a name="can-i-upgrade-old-project"></a>Mohu upgradovat starý projekt?

Projekty z předchozí verze Migrace Azure nelze aktualizovat. Je třeba [vytvořit nový projekt](how-to-add-tool-first-time.md)a přidat do něj nástroje.

## <a name="cant-create-a-project"></a>Nelze vytvořit projekt

Pokud se pokusíte vytvořit projekt a dojde k chybě nasazení:

- Zkuste vytvořit projekt znovu v případě, že se jedná o přechodnou chybu. V **nasazení**klikněte na **Znovu nasadit** a zkuste to znovu.
- Zkontrolujte, zda máte v předplatném oprávnění přispěvatele nebo vlastníka.
- Pokud nasazujete v nově přidané zeměpisné oblasti, počkejte krátkou dobu a zkuste to znovu.
- Pokud se zobrazí chyba "Požadavky musí obsahovat hlavičky identity uživatele", může to znamenat, že nemáte přístup k tenantovi Azure Active Directory (Azure AD) organizace. V tomto případě:
    - Když jste poprvé přidáni do klienta Azure AD, obdržíte e-mailovou pozvánku k připojení k tenantovi.
    - Přijměte pozvánku, která má být přidána do tenanta.
    - Pokud e-mail nevidíte, kontaktujte uživatele s přístupem k tenantovi a požádejte ho, aby vám [pozvánku znovu odeslal.](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)
    - Po obdržení e-mailu s pozvánkou jej otevřete a vyberte odkaz pro přijetí pozvánky. Potom se odhlaste z webu Azure Portal a znovu se přihlaste. (aktualizace prohlížeče nebude fungovat.) Potom můžete začít vytvářet projekt migrace.

## <a name="how-do-i-delete-a-project"></a>Jak odstranit projekt

[Podle těchto pokynů](create-manage-projects.md#delete-a-project) projekt odstraňte. Všimněte si, že při odstranění projektu, projekt a metadata o zjištěných počítačích v projektu jsou odstraněny.

## <a name="added-tools-dont-show"></a>Přidané nástroje se nezobrazují

Ujistěte se, že máte vybrán správný projekt. V centru Azure Migrate > **servery** nebo v **databázích**klikněte na **Změnit** vedle **projektu (Změnit)** v pravém horním rohu obrazovky. Zvolte správné předplatné a název projektu > **OK**. Stránka by se měla aktualizovat pomocí přidaných nástrojů vybraného projektu.

## <a name="next-steps"></a>Další kroky

Přidejte nástroje [pro hodnocení](how-to-assess.md) nebo [migraci](how-to-migrate.md) do projektů Migrace Azure.
---
title: Řešení problémů s projekty Azure Migrate
description: Pomáhá řešit problémy s vytvářením a správou Azure Migrate projektů.
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: f2245e13f348706f662055408196ea7a26484811
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92314647"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Řešení problémů s projekty Azure Migrate

Tento článek vám pomůže při řešení potíží při vytváření a správě [Azure Migratech](migrate-services-overview.md) projektů.

## <a name="how-to-add-new-project"></a>Jak přidat nový projekt?

V předplatném můžete mít více Azure Migratech projektů. [Naučte](how-to-add-tool-first-time.md) se, jak poprvé vytvořit projekt, nebo [Přidat další](create-manage-projects.md#create-additional-projects) projekty.

## <a name="what-azure-permissions-are-needed"></a>Jaká oprávnění Azure potřebujete?

Chcete-li vytvořit projekt Azure Migrate, potřebujete oprávnění přispěvatele nebo vlastníka v rámci předplatného.

## <a name="cant-find-a-project"></a>Nejde najít projekt.

Hledání stávajícího Azure Migrate projektu závisí na tom, zda používáte aktuální nebo starou verzi Azure Migrate. [Sledujte](create-manage-projects.md#find-a-project).


## <a name="cant-find-a-geography"></a>Nemůžete najít geografickou oblast.

V podporovaných geografických oblastech pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government)můžete vytvořit Azure Migrate projekt.

## <a name="what-are-vm-limits"></a>Co jsou omezení virtuálních počítačů?

V jednom projektu můžete vyhodnotit až 35 000 virtuálních počítačů VMware nebo až 35 000 virtuálních počítačů Hyper-V. Projekt může zahrnovat virtuální počítače VMware i virtuální počítače Hyper-V, a to až do limitů hodnocení.

## <a name="can-i-upgrade-old-project"></a>Můžu upgradovat starý projekt?

Projekty z předchozí verze Azure Migrate nelze aktualizovat. Musíte [vytvořit nový projekt](how-to-add-tool-first-time.md)a přidat do něj nástroje.

## <a name="cant-create-a-project"></a>Nejde vytvořit projekt.

Pokud se pokusíte vytvořit projekt a dojde k chybě při nasazení:

- Zkuste projekt znovu vytvořit pro případ, že se jedná o přechodnou chybu. V **nasazení**klikněte na **znovu nasadit** a zkuste to znovu.
- Ověřte, jestli máte v předplatném oprávnění Přispěvatel nebo Owner.
- Pokud nasazujete v nově přidané geografické oblasti, počkejte krátkou dobu a pak to zkuste znovu.
- Pokud se zobrazí chybová zpráva "požadavky musí obsahovat hlavičky identity uživatele", může to znamenat, že nemáte přístup k tenantovi Azure Active Directory (Azure AD) organizace. V tomto případě:
    - Při prvním přidání do tenanta Azure AD obdržíte e-mailovou pozvánku pro připojení ke klientovi.
    - Přijměte pozvánku, která se má přidat do tenanta.
    - Pokud se e-mail nezobrazuje, obraťte se na uživatele s přístupem k tenantovi a požádejte ho, aby [pozvánku poslal znovu](../active-directory/external-identities/add-users-administrator.md#resend-invitations-to-guest-users) .
    - Po přijetí e-mailu s pozvánkou otevřete ho a vyberte odkaz pro přijetí pozvánky. Pak se odhlaste ze Azure Portal a znovu se přihlaste. (obnovení prohlížeče nebude fungovat.) Pak můžete začít vytvářet projekt migrace.

## <a name="how-do-i-delete-a-project"></a>Návody odstranit projekt

Chcete-li odstranit projekt, [postupujte podle těchto pokynů](create-manage-projects.md#delete-a-project) . Všimněte si, že při odstranění projektu se odstraní projekt i metadata o zjištěných počítačích v projektu.

## <a name="added-tools-dont-show"></a>Přidané nástroje se nezobrazují

Ujistěte se, že máte vybraný správný projekt. V centru Azure Migrate > **serverech** nebo v části **databáze**klikněte v pravém horním rohu obrazovky na **změnit** u pole **migrovat projekt (změnit)** . Vyberte správné předplatné a název projektu > **OK**. Stránka by se měla aktualizovat pomocí přidaných nástrojů vybraného projektu.

## <a name="next-steps"></a>Další kroky

Přidejte nástroje pro [posouzení](how-to-assess.md) nebo [migraci](how-to-migrate.md) do Azure Migrate projektů.
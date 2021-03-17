---
title: Obnovení odstraněného účtu úložiště
titleSuffix: Azure Storage
description: Naučte se, jak obnovit odstraněný účet úložiště v rámci Azure Portal.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f57cd3361d7888d9d7f747955257d96282274fd6
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357341"
---
# <a name="recover-a-deleted-storage-account"></a>Obnovení odstraněného účtu úložiště

Odstraněný účet úložiště se v některých případech může v rámci Azure Portal obnovit. Chcete-li obnovit účet úložiště, musí být splněny následující podmínky:

- Účet úložiště se odstranil během posledních 14 dnů.
- Účet úložiště se vytvořil pomocí modelu nasazení Azure Resource Manager.
- Od odstranění původního účtu se nevytvořil nový účet úložiště se stejným názvem.

Než se pokusíte obnovit odstraněný účet úložiště, ujistěte se, že skupina prostředků tohoto účtu existuje. Pokud se skupina prostředků odstranila, musíte ji znovu vytvořit. Obnovování skupiny prostředků není možné. Další informace o nástroji najdete v tématu [Správa skupin prostředků](../../azure-resource-manager/management/manage-resource-groups-portal.md).

Pokud odstraněný účet úložiště používal klíče spravované zákazníkem se Azure Key Vault a odstranil se i Trezor klíčů, musíte před obnovením účtu úložiště obnovit Trezor klíčů. Další informace najdete v tématu [Přehled služby Azure Key Vault Recovery](../../key-vault/general/key-vault-recovery.md).

> [!IMPORTANT]
> Obnovení odstraněného účtu úložiště není zaručené. Zotavení je snaha o nejlepší úsilí. Microsoft doporučuje uzamknout prostředky, aby nedocházelo k nechtěnému odstranění účtu. Další informace o zámkech prostředků najdete v tématu [uzamčení prostředků, aby se zabránilo změnám](../../azure-resource-manager/management/lock-resources.md).
>
> Dalším osvědčeným postupem, jak se vyhnout náhodnému odstranění účtu, je omezit počet uživatelů, kteří mají oprávnění k odstranění účtu prostřednictvím řízení přístupu na základě role (Azure RBAC). Další informace najdete v tématu [osvědčené postupy pro službu Azure RBAC](../../role-based-access-control/best-practices.md).

## <a name="recover-a-deleted-account-from-the-azure-portal"></a>Obnovení odstraněného účtu z Azure Portal

Pokud chcete obnovit odstraněný účet úložiště v jiném účtu úložiště, postupujte takto:

1. Přejděte na stránku Přehled pro existující účet úložiště v Azure Portal.
1. V části **Podpora a řešení potíží** vyberte **obnovit odstraněný účet**.
1. V rozevíracím seznamu vyberte účet, který chcete obnovit, jak je znázorněno na následujícím obrázku. Pokud není účet úložiště, který chcete obnovit, v rozevíracím seznamu, pak ho nelze obnovit.

    :::image type="content" source="media/storage-account-recover/recover-account-portal.png" alt-text="Snímek obrazovky, který ukazuje, jak obnovit účet úložiště v Azure Portal":::

1. Kliknutím na tlačítko **obnovit** účet obnovte. Na portálu se zobrazí oznámení o tom, že probíhá obnovení.

## <a name="recover-a-deleted-account-via-a-support-ticket"></a>Obnovení odstraněného účtu pomocí lístku podpory

1. V Azure Portal přejděte na pomoc a **Podpora**.
1. Vyberte **Nová žádost o podporu**.
1. Na kartě **základy** v poli **typ problému** vyberte možnost **technický**.
1. V poli **předplatné** vyberte předplatné, které obsahovalo odstraněný účet úložiště.
1. V poli **Služba** vyberte **Správa účtů úložiště**.
1. V poli **prostředek** vyberte libovolný prostředek účtu úložiště. Odstraněný účet úložiště se v seznamu nezobrazí.
1. Přidejte stručný souhrn problému.
1. V poli **typ problému** vyberte možnost **Odstranit a obnovit**.
1. V poli **podtyp problému** vyberte **obnovit odstraněný účet úložiště**. Následující obrázek ukazuje příklad vyplňování karty **základy** :

    :::image type="content" source="media/storage-account-recover/recover-account-support-basics.png" alt-text="Snímek obrazovky s informacemi o tom, jak obnovit účet úložiště prostřednictvím karty podpory pro základy lístků":::

1. Potom přejděte na kartu **řešení** a vyberte možnost **obnovení účtu úložiště spravovaného zákazníkem**, jak je znázorněno na následujícím obrázku:

    :::image type="content" source="media/storage-account-recover/recover-account-support-solutions.png" alt-text="Snímek obrazovky s informacemi o tom, jak obnovit účet úložiště prostřednictvím karty podpory – lístek řešení":::

1. V rozevíracím seznamu vyberte účet, který chcete obnovit, jak je znázorněno na následujícím obrázku. Pokud není účet úložiště, který chcete obnovit, v rozevíracím seznamu, pak ho nelze obnovit.

    :::image type="content" source="media/storage-account-recover/recover-account-support.png" alt-text="Snímek obrazovky, který ukazuje, jak obnovit účet úložiště prostřednictvím lístku podpory":::

1. Kliknutím na tlačítko **obnovit** účet obnovte. Na portálu se zobrazí oznámení o tom, že probíhá obnovení.

## <a name="next-steps"></a>Další kroky

- [Přehled účtu úložiště](storage-account-overview.md)
- [Vytvoření účtu úložiště](storage-account-create.md)
- [Upgrade na účet úložiště pro obecné účely verze 2](storage-account-upgrade.md)
- [Přesunutí účtu Azure Storage do jiné oblasti](storage-account-move.md)
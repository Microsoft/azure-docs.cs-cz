---
title: Kurz – vytvoření sady replik v Azure AD Domain Services | Microsoft Docs
description: Naučte se vytvářet a používat sady replik v Azure Portal pro odolnost služby s Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: justinha
ms.openlocfilehash: e8887cc39b48f090ff223e5e83c13d65b921dc0b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687494"
---
# <a name="tutorial-create-and-use-replica-sets-for-resiliency-or-geolocation-in-azure-active-directory-domain-services"></a>Kurz: vytvoření a použití sad replik pro zajištění odolnosti nebo geografického umístění v Azure Active Directory Domain Services

Pokud chcete zlepšit odolnost služby Azure Active Directory Domain Services (Azure služba AD DS) spravované domény nebo ji nasaďte do dalších geografických umístění blízko vašich aplikací, můžete použít *sady replik*. Každý obor názvů spravované domény Azure služba AD DS, jako je například *aaddscontoso.com*, obsahuje jednu počáteční sadu replik. Možnost vytvářet další sady replik v jiných oblastech Azure poskytuje geografickou odolnost pro spravovanou doménu.

Sadu replik můžete přidat do kterékoli partnerské virtuální sítě v libovolné oblasti Azure, která podporuje Azure služba AD DS.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Pochopení požadavků na virtuální síť
> * Vytvoření sady replik
> * Odstranění sady replik

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Azure Active Directory Domain Services spravovaná doména vytvořená pomocí modelu nasazení Azure Resource Manager a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby [vytvořte a nakonfigurujte Azure Active Directory Domain Services spravovanou doménu][tutorial-create-instance].

    > [!IMPORTANT]
    > Spravované domény vytvořené pomocí modelu nasazení Classic nemůžou používat sady replik. Pro spravovanou doménu je také nutné použít minimálně jednotku SKU *Enterprise* . V případě potřeby [změňte skladovou položku pro spravovanou doménu][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V tomto kurzu vytvoříte a spravujete sady replik pomocí Azure Portal. Chcete-li začít, nejprve se přihlaste k [Azure Portal](https://portal.azure.com).

## <a name="networking-considerations"></a>Aspekty sítí

Virtuální sítě, které hostují sady replik, musí být schopné vzájemně komunikovat. Aplikace a služby, které závisí na Azure služba AD DS, potřebují taky síťové připojení k virtuálním sítím hostujícím sady replik. Partnerský vztah virtuálních sítí Azure by měl být nakonfigurovaný mezi všemi virtuálními sítěmi, aby se vytvořila plně síť s mřížkou. Tyto partnerské vztahy umožňují efektivní replikaci mezi lokalitami mezi sadami replik.

Než budete moct použít sady replik v Azure služba AD DS, přečtěte si následující požadavky na virtuální síť Azure:

* Nepoužívejte překrývající se adresní prostory IP adres, aby bylo možné vytvořit partnerský vztah virtuální sítě a směrování.
* Vytvořte podsítě s dostatkem IP adres pro podporu vašeho scénáře.
* Ujistěte se, že je v Azure služba AD DS vlastní podsíť. Nesdílejte tuto podsíť virtuální sítě s virtuálními počítači a službami aplikací.
* Partnerské virtuální sítě nejsou přenosné.

> [!TIP]
> Když vytvoříte sadu replik v Azure Portal, vytvoří se pro vás síťové partnerské vztahy mezi virtuálními sítěmi.
>
> V případě potřeby můžete vytvořit virtuální síť a podsíť, když přidáte sadu replik v Azure Portal. Nebo můžete zvolit existující prostředky virtuální sítě v cílové oblasti pro sadu replik a nechat vytvořit partnerské vztahy automaticky, pokud ještě neexistují.

## <a name="create-a-replica-set"></a>Vytvoření sady replik

Při vytváření spravované domény, jako je třeba *aaddscontoso.com*, se vytvoří počáteční sada replik. Další sady replik sdílejí stejný obor názvů a konfiguraci. Změny v Azure služba AD DS, včetně konfigurace, identity uživatele a přihlašovacích údajů, skupin, objektů zásad skupiny, počítačových objektů a dalších změn, se aplikují na všechny sady replik ve spravované doméně pomocí replikace služba AD DS.

V tomto kurzu vytvoříte další sadu replik v oblasti Azure odlišnou od počáteční sady replik služby Azure služba AD DS.

Chcete-li vytvořit další sadu replik, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **Azure AD Domain Services**.
1. Vyberte spravovanou doménu, například *aaddscontoso.com*.
1. Na levé straně vyberte **sady replik**. Každá spravovaná doména obsahuje jednu počáteční sadu replik ve vybrané oblasti, jak je znázorněno na následujícím ukázkovém snímku obrazovky:

    ![Ukázkový snímek obrazovky pro zobrazení a přidání sady replik v Azure Portal](./media/tutorial-create-replica-set/replica-set-list.png)

    Pokud chcete vytvořit další sadu replik, vyberte **+ Přidat**.

1. V okně *Přidat sadu replik* vyberte cílovou oblast, například *východní USA*.

    Vyberte virtuální síť v cílové oblasti, třeba *VNet-eastus*, a pak zvolte podsíť, třeba *aadds-Subnet*. V případě potřeby vyberte **vytvořit novou** a přidejte do cílové oblasti virtuální síť a potom pomocí **možnosti spravovat** vytvořte podsíť pro Azure služba AD DS.

    Pokud ještě neexistují, partnerské vztahy virtuálních sítí Azure se automaticky vytvoří mezi virtuální sítí vaší existující spravované domény a cílovou virtuální sítí.

    Následující ukázkový snímek obrazovky ukazuje proces vytvoření nové sady replik v *východní USA*:

    ![Ukázkový snímek obrazovky pro vytvoření sady replik v Azure Portal](./media/tutorial-create-replica-set/create-replica-set.png)

1. Až budete připraveni, vyberte **Uložit**.

Proces vytvoření sady replik trvá určitou dobu, protože se prostředky vytvoří v cílové oblasti. Samotná spravovaná doména se pak replikuje pomocí replikace služba AD DS.

Sada replik sestav sestaví jako *zřizování* jako nasazení pokračuje, jak je znázorněno na následujícím ukázkovém snímku obrazovky. Po dokončení se sada repliky zobrazí jako *spuštěná*.

![Ukázkový snímek obrazovky se stavem nasazení sady replik v Azure Portal](./media/tutorial-create-replica-set/replica-set-provisioning.png)

## <a name="delete-a-replica-set"></a>Odstranění sady replik

Spravovaná doména je aktuálně omezená na čtyři repliky – počáteční sada replik a tři další sady replik. Pokud už sadu replik již nepotřebujete nebo pokud chcete vytvořit sadu replik v jiné oblasti, můžete odstranit nepotřebné sady replik.

> [!IMPORTANT]
> Nemůžete odstranit poslední sadu replik ve spravované doméně.

Chcete-li odstranit sadu replik, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **Azure AD Domain Services**.
1. Vyberte spravovanou doménu, například *aaddscontoso.com*.
1. Na levé straně vyberte **sady replik**. V seznamu sad replik vyberte kontextovou nabídku **...** vedle sady replik, kterou chcete odstranit.
1. V místní nabídce vyberte **Odstranit** a pak potvrďte, že chcete odstranit sadu replik.

> [!NOTE]
> Odstranění sady replik může být časově náročná operace.

Pokud již nepotřebujete virtuální síť nebo partnerský vztah používaný sadou replik, můžete tyto prostředky také odstranit. Ujistěte se, že žádné další prostředky aplikace v jiné oblasti nepotřebují připojení k síti, než je odstraníte.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace partnerského vztahu virtuálních sítí
> * Vytvoření sady replik v jiné geografické oblasti
> * Odstranění sady replik

Další koncepční informace najdete v tématu Jak sady replik fungují v Azure služba AD DS.

> [!div class="nextstepaction"]
> [Koncepty a funkce sady replik][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md

---
title: Odstranit Azure Active Directory Domain Services | Microsoft Docs
description: Přečtěte si, jak zakázat nebo odstranit Azure Active Directory Domain Services spravované domény pomocí Azure Portal
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 5844fca371680ce02e6628d9cacbf50ebe4c8ed0
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84734822"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Odstranění Azure Active Directory Domain Services spravované domény pomocí Azure Portal

Pokud už spravovanou doménu nepotřebujete, můžete ji odstranit Azure Active Directory Domain Services (Azure služba AD DS) spravované domény. Neexistuje možnost vypnout nebo dočasně zakázat spravovanou doménu Azure služba AD DS. Odstranění spravované domény neodstraní nebo jinak nepříznivě ovlivní tenanta Azure AD. V tomto článku se dozvíte, jak pomocí Azure Portal odstranit spravovanou doménu.

> [!WARNING]
> **Odstranění je trvalé a nelze je vrátit zpět.**
> Při odstranění spravované domény dojde k následujícím krokům:
>   * Řadiče domény pro spravovanou doménu se zruší a odeberou se z této virtuální sítě.
>   * Data ve spravované doméně se trvale odstraní. Tato data zahrnují vlastní organizační jednotky, objekty zásad skupiny, vlastní záznamy DNS, instanční objekty, účty gMSA atd., které jste vytvořili.
>   * Počítače připojené ke spravované doméně ztratily vztah důvěryhodnosti s doménou a musí být z domény odpojování.
>       * K těmto počítačům se nemůžete přihlásit pomocí podnikových přihlašovacích údajů služby AD. Místo toho musíte pro tento počítač použít přihlašovací údaje místního správce.

## <a name="delete-the-managed-domain"></a>Odstranit spravovanou doménu

Pokud chcete odstranit spravovanou doménu, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **Azure AD Domain Services**.
1. Vyberte název spravované domény, například *aaddscontoso.com*.
1. Na stránce **Přehled** vyberte **Odstranit**. Odstranění potvrďte tak, že znovu zadáte název domény spravované domény a pak vyberete **Odstranit**.

Odstranění spravované domény může trvat 15-20 minut nebo déle.

## <a name="next-steps"></a>Další kroky

Zvažte [sdílení zpětné vazby][feedback] k funkcím, které byste chtěli vidět v Azure služba AD DS.

Pokud chcete začít se službou Azure služba AD DS znovu, přečtěte si téma [Vytvoření a konfigurace Azure Active Directory Domain Services spravované domény][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md

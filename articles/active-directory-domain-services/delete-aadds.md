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
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: e1836f91b8afc1bb4f5b7e141949f3724c57c857
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614033"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Odstranění Azure Active Directory Domain Services spravované domény pomocí Azure Portal

Pokud už nepotřebujete spravovanou doménu, můžete instanci Azure Active Directory Domain Services (Azure služba AD DS) odstranit. Neexistuje možnost vypnout nebo dočasně zakázat spravovanou doménu Azure služba AD DS. Odstranění spravované domény Azure služba AD DS neodstraní nebo jinak nepříznivě ovlivní tenanta Azure AD. V tomto článku se dozvíte, jak použít Azure Portal k odstranění spravované domény Azure služba AD DS.

> [!WARNING]
> **Odstranění je trvalé a nelze je vrátit zpět.**
> Při odstranění spravované domény služby Azure služba AD DS dojde k následujícím krokům:
>   * Řadiče domény pro spravovanou doménu se zruší a odeberou se z této virtuální sítě.
>   * Data ve spravované doméně se trvale odstraní. Tato data zahrnují vlastní organizační jednotky, objekty zásad skupiny, vlastní záznamy DNS, instanční objekty, účty gMSA atd., které jste vytvořili.
>   * Počítače připojené ke spravované doméně ztratily vztah důvěryhodnosti s doménou a musí být z domény odpojování.
>       * K těmto počítačům se nemůžete přihlásit pomocí podnikových přihlašovacích údajů služby AD. Místo toho musíte pro tento počítač použít přihlašovací údaje místního správce.

## <a name="delete-the-managed-domain"></a>Odstranit spravovanou doménu

Pokud chcete odstranit spravovanou doménu Azure služba AD DS, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **Azure AD Domain Services**.
1. Vyberte název spravované domény Azure služba AD DS, například *aaddscontoso.com*.
1. Na stránce **Přehled** vyberte **Odstranit**. Odstranění potvrďte tak, že znovu zadáte název domény spravované domény a pak vyberete **Odstranit**.

Odstranění spravované domény Azure služba AD DS může trvat 15-20 minut nebo déle.

## <a name="next-steps"></a>Další kroky

Zvažte [sdílení zpětné vazby][feedback] k funkcím, které byste chtěli vidět v Azure služba AD DS.

Pokud chcete začít s Azure služba AD DS znovu, přečtěte si téma [Vytvoření a konfigurace instance Azure Active Directory Domain Services][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md

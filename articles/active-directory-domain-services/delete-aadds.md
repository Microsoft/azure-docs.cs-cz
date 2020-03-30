---
title: Odstranění služby Azure Active Directory Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak zakázat nebo odstranit spravovanou doménu Služby Azure Active Directory Domain Services pomocí webu Azure Portal
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614033"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Odstranění spravované domény služby Azure Active Directory Domain Services pomocí webu Azure Portal

Pokud už nepotřebujete spravovanou doménu, můžete odstranit instanci Služby Azure Active Directory Domain Services (Azure AD DS). Neexistuje žádná možnost vypnout nebo dočasně zakázat spravovanou doménu Azure AD DS. Odstranění spravované domény Azure AD DS neodstraní ani jinak nepříznivě neovlivní klienta Azure AD. Tento článek ukazuje, jak pomocí portálu Azure k odstranění spravované domény Azure AD DS.

> [!WARNING]
> **Odstranění je trvalé a nelze jej vrátit zpět.**
> Když odstraníte spravovanou doménu Azure AD DS, dojde k následujícím krokům:
>   * Řadiče domény pro spravovanou doménu jsou zrušeny a odebrány z virtuální sítě.
>   * Data ve spravované doméně se trvale odstraní. Tato data zahrnují vlastní vous, objekty zásad skupiny, vlastní záznamy DNS, instanční objekty, GMSA atd., které jste vytvořili.
>   * Počítače připojované ke spravované doméně ztratí svůj vztah důvěryhodnosti k doméně a je třeba je od ní odpojit.
>       * K těmto počítačům se nelze přihlásit pomocí podnikových přihlašovacích údajů služby AD. Místo toho je nutné použít pověření místního správce pro počítač.

## <a name="delete-the-managed-domain"></a>Odstranění spravované domény

Chcete-li odstranit spravovanou doménu Azure AD DS, proveďte následující kroky:

1. Na webu Azure Portal vyhledejte a vyberte **služby Domény Azure AD**.
1. Vyberte název spravované domény Azure AD DS, například *aaddscontoso.com*.
1. Na stránce **Přehled** vyberte **Odstranit**. Chcete-li odstranění potvrdit, zadejte znovu název domény spravované domény a vyberte **odstranit**.

Odstranění spravované domény Azure AD DS může trvat 15 až 20 minut nebo déle.

## <a name="next-steps"></a>Další kroky

Zvažte [sdílení zpětné vazby][feedback] pro funkce, které chcete zobrazit ve službě Azure AD DS.

Pokud chcete začít znovu s Azure AD DS, přečtěte si informace [o vytvoření a konfiguraci instance služby Azure Active Directory Domain Services][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md

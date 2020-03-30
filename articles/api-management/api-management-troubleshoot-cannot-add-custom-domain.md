---
title: Vlastní doménu nelze přidat pomocí certifikátu Trezoru klíčů.
titleSuffix: Azure API Management
description: Zjistěte, jak vyřešit problém, ve kterém nemůžete přidat vlastní doménu ve správě rozhraní Azure API pomocí certifikátu trezoru klíčů.
services: api-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: a09c15466a4a9f62b2696b087cb7ab23cc767379
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430574"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>Aktualizace názvů hostitelů služby Správa rozhraní API se nezdařila.

Tento článek popisuje chybu "Nepodařilo se aktualizovat názvy hostitelů služby API Management", ke které může dojít při přidání vlastní domény pro službu Azure API Management. Tento článek obsahuje postup řešení potíží, který vám pomůže problém vyřešit.

## <a name="symptoms"></a>Příznaky

Při pokusu o přidání vlastní domény pro službu api management pomocí certifikátu z trezoru klíčů Azure se zobrazí následující chybová zpráva:

- Aktualizace názvů hostitelů služby Správa rozhraní API se nezdařila. Požadavek nahttps://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0prostředek ' ' se nezdařil o statuscode: Zakázáno pro RequestId: . Zpráva o výjimce: Operace vrátila neplatný stavový kód Zakázáno.

## <a name="cause"></a>Příčina

Služba API Management nemá oprávnění k přístupu k trezoru klíčů, který se pokoušíte použít pro vlastní doménu.

## <a name="solution"></a>Řešení

Pokud chcete tento problém vyřešit, postupujte následovně:

1. Přejděte na [portál Azure](Https://portal.azure.com), vyberte instanci správy rozhraní API a pak vyberte **Spravované identity**. Ujistěte se, že je možnost **Registrovat pomocí služby Azure Active Directory** nastavená na **ano**. 
    ![Registrace u ředitele Azure Active Director](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. Na webu Azure Portal otevřete službu **Trezory klíčů** a vyberte trezor klíčů, který se pokoušíte použít pro vlastní doménu.
1. Vyberte **zásady přístupu**a zkontrolujte, zda existuje instanční objekt služby, který odpovídá názvu instance služby Správa rozhraní API. Pokud existuje, vyberte instanční objekt a ujistěte se, že má **získat** oprávnění uvedené pod **tajné oprávnění**.  
    ![Přidání zásad přístupu pro instanční objekt](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. Pokud služba Správa rozhraní API není v seznamu, vyberte **Přidat zásady přístupu**a vytvořte následující zásady přístupu:
    - **Konfigurovat ze šablony**: Žádné
    - **Vyberte hlavní:** Vyhledejte název služby API Management a vyberte ji ze seznamu.
    - **Klíčová oprávnění**: Žádná
    - **Tajná oprávnění**: Získat
    - **Oprávnění certifikátu**: Žádná
1. Chcete-li vytvořit zásady přístupu, vyberte **ok.**
1. Chcete-li změny uložit, vyberte **uložit.**

Zkontrolujte, zda byl problém vyřešen. Chcete-li to provést, zkuste vytvořit vlastní doménu ve službě Api Management pomocí certifikátu Trezor klíčů.

## <a name="next-steps"></a>Další kroky
Další informace o službě API Management:

- Podívejte se na další [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o správě rozhraní API.
* Další způsoby zabezpečení back-endové služby naleznete v [tématu Vzájemné ověřování certifikátů](api-management-howto-mutual-certificates.md).

* [Vytvořte instanci služby Správa rozhraní API](get-started-create-service-instance.md).
* [Spravujte své první rozhraní API](import-and-publish.md).
---
title: Nelze přidat vlastní doménu pomocí Key Vaultho certifikátu v Azure API Management | Microsoft Docs
description: Naučte se řešit potíže, při kterých nemůžete do Azure API Management přidat vlastní doménu pomocí certifikátu trezoru klíčů.
services: api-management
documentationcenter: ''
author: genlin
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: 30dfd864c6c237024c72325da813691ac5043bd0
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072095"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>Nepovedlo se aktualizovat názvy hostitelů služby API Management.

Tento článek popisuje chybu "nepovedlo se aktualizovat názvy hostitelů služby API Management), se kterými se můžete setkat při přidávání vlastní domény pro službu Azure API Management. Tento článek popisuje kroky pro řešení potíží, které vám pomůžou tento problém vyřešit.

## <a name="symptoms"></a>Příznaky

Při pokusu o přidání vlastní domény pro službu API Management pomocí certifikátu z Azure Key Vault se zobrazí následující chybová zpráva:

- Nepovedlo se aktualizovat názvy hostitelů služby API Management. Požadavek na prostředek https://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0 se nezdařil s chybou StatusCode: Zakázáno pro RequestId:. Zpráva o výjimce: Operace vrátila neplatný stavový kód zakázáno.

## <a name="cause"></a>Příčina

Služba API Management nemá oprávnění pro přístup k trezoru klíčů, který se pokoušíte použít pro vlastní doménu.

## <a name="solution"></a>Řešení

Pokud chcete tento problém vyřešit, postupujte následovně:

1. Otevřete [Azure Portal](Https://portal.azure.com), vyberte svou instanci API Management a pak vyberte **spravované identity**. Ujistěte se, že možnost **registrovat s Azure Active Directory** je nastavená na **hodnotu Ano**. 
    ![Registrace ve službě Azure Active Director](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. V Azure Portal otevřete službu trezory **klíčů** a vyberte Trezor klíčů, který se pokoušíte použít pro vlastní doménu.
1. Vyberte **zásady přístupu**a ověřte, jestli existuje instanční objekt, který odpovídá názvu instance služby API Management. V takovém případě vyberte instanční objekt a ujistěte se, že má oprávnění **získat** uvedené v části **oprávnění tajného**kódu.  
    ![Přidání zásad přístupu pro instanční objekt](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. Pokud služba API Management není v seznamu, vyberte **Přidat zásadu přístupu**a pak vytvořte následující zásady přístupu:
    - **Konfigurovat ze šablony**: Žádné
    - **Vyberte objekt zabezpečení**: Vyhledejte název služby API Management a vyberte ji ze seznamu.
    - **Klíčová oprávnění**: Žádné
    - **Oprávnění tajného**kódu: Získat
    - **Oprávnění certifikátu**: Žádné
1. Vyberte **OK** a vytvořte zásady přístupu.
1. Vyberte **Uložit** a uložte změny.

Zkontrolujte, zda byl problém vyřešen. Chcete-li to provést, zkuste vytvořit vlastní doménu ve službě API Management pomocí certifikátu Key Vault.

## <a name="next-steps"></a>Další postup
Další informace o službě API Management Service:

- Podívejte se na další [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o API Management.
* Další způsoby zabezpečení back-endové služby najdete v tématu [vzájemné ověřování certifikátů](api-management-howto-mutual-certificates.md).

* [Vytvořte instanci služby API Management](get-started-create-service-instance.md).
* [Spravujte své první rozhraní API](import-and-publish.md).
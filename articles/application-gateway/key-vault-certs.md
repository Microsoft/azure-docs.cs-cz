---
title: Ukončení protokolu SSL s využitím služby Key Vault certifikátů
description: Zjistěte, jak integrace Azure application gateway se službou Key Vault pro certifikáty serveru, které jsou připojeny k naslouchací procesy HTTPS povolené.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 37707d56caabf0ae8b0020eb8714245a27501ea6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696499"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Ukončení protokolu SSL s využitím služby Key Vault certifikátů

[Služba Azure Key Vault](../key-vault/key-vault-whatis.md) je spravovaná platforma tajných kódů úložiště můžete použít k ochraně tajných klíčů, klíče a certifikáty SSL. Služba Application Gateway podporuje integraci se službou Key Vault (ve verzi public preview) pro server certifikáty, které jsou připojeny k naslouchací procesy HTTPS povolena. Tato podpora je omezena na v2 SKU služby Application Gateway.

> [!IMPORTANT]
> Integrace Application Gateway Key Vault je aktuálně ve verzi public preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Existují dva modely pro ukončení protokolu SSL v této veřejné předběžné verzi:

- Můžete explicitně uvést certifikáty SSL, které jsou připojené k naslouchacímu procesu. Toto je tradiční model předávání certifikáty SSL k Application Gateway pro ukončení protokolu SSL.
- Volitelně můžete zadat odkaz na existující certifikát služby Key Vault nebo tajný klíč během HTTPS povolený naslouchacího procesu vytváření.

Existuje mnoho výhod integrace služby Key Vault, včetně:

- Silnější zabezpečení, protože certifikáty SSL nezabývá přímo vývojovým týmem aplikace. Integrace se službou Key Vault umožňuje samostatné bezpečnostní tým zřízení, řízení životního cyklu a udělit příslušná oprávnění a vyberte Application Gateway přístup certifikátů uložených v Key Vault.
- Podpora pro import existujících certifikátů do služby Key Vault nebo vytvořit a spravovat nové certifikáty s některou z důvěryhodných partnerů služby Key Vault pomocí rozhraní API Key Vault.
- Podpora pro certifikátů uložených v Key Vault a automaticky obnovovat.

Služba Application Gateway aktuálně podporuje pouze certifikáty softwaru ověřit. Certifikáty ověřené modulu Hardwarového zabezpečení hardwaru nejsou podporovány. Jakmile služba Application Gateway je konfigurován pro použití služby Key Vault certifikáty, její instance načíst certifikát ze služby Key Vault a nainstalovat místně pro ukončení protokolu SSL. Instance také pravidelně dotazovat služby Key Vault v 24hodinového intervalu pro načtení obnovené verzi certifikátu, pokud existuje. Pokud aktualizovaný certifikát není nalezen, je automaticky otočit certifikát SSL, který je aktuálně přidružený naslouchací proces HTTPS.

## <a name="how-it-works"></a>Jak to funguje

Integrace se službou Key Vault vyžaduje proces konfigurace třech krocích:

1. **Vytvoření spravované identity přiřazené uživateli**

   Musíte vytvořit nebo opětovné použití existujícího uživatele přiřazeny spravovanou identitu, která Application Gateway používá k načtení certifikátů ze služby Key Vault za vás. Další informace najdete v tématu [co je spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md) Tento krok vytvoří novou identitu v tenantovi Azure AD, která je důvěryhodná pro předplatné použité k vytvoření identity.
1. **Konfigurovat službu Key Vault**

   Potom musí buď import nebo vytvoření nového certifikátu ve službě Key Vault používané aplikacemi s prostřednictvím služby Application Gateway. Key Vault tajný klíč uložen jako bez hesla base 64 kódovaných souboru PFX můžete použít také v tomto kroku. Použití typu certifikátu se upřednostňuje kvůli automatické obnovování možnosti dostupné v rámci objekty typu certifikátu ve službě Key Vault. Po vytvoření certifikátu nebo tajný klíč zásady přístupu musí být definován ve službě Key Vault umožňuje identitě, kterou chcete udělit *získat* přístup k načtení tajný kód.

1. **Konfigurace aplikační brány**

   Po dokončení předchozí dva kroky jsou můžete zřizovat nebo upravit stávající aplikační brány pro použití spravované identity přiřazené uživateli. Můžete také nakonfigurovat certifikát SSL pro naslouchací proces protokolu HTTP tak, aby odkazoval úplný identifikátor URI služby Key Vault pro certifikát nebo ID tajného kódu.

![Certifikáty služby Key Vault](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Další postup

[Konfiguraci ukončení protokolu SSL s využitím certifikátů služby Key Vault pomocí Azure Powershellu](configure-keyvault-ps.md).
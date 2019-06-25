---
title: Ukončení protokolu SSL s využitím certifikátů Azure Key Vault
description: Zjistěte, jak integrovat Azure Application Gateway s využitím služby Key Vault pro certifikáty serveru, které jsou připojeny k naslouchacích procesů s povolenou komunikací HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 18af315c58c838a7237acfbcc32f622a0edbd3b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827624"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Ukončení protokolu SSL s využitím služby Key Vault certifikátů

[Služba Azure Key Vault](../key-vault/key-vault-whatis.md) je spravované platformy tajného klíče úložiště, můžete použít k ochraně tajných klíčů, klíče a certifikáty SSL. Azure Application Gateway podporuje integraci se službou Key Vault (ve verzi public preview) pro certifikáty serveru, které jsou připojeny k naslouchacích procesů s povolenou komunikací HTTPS. Tato podpora je omezena na v2 SKU služby Application Gateway.

> [!IMPORTANT]
> Integrace Application Gateway s využitím služby Key Vault je aktuálně ve verzi public preview. V této verzi preview je k dispozici bez smlouvu o úrovni služeb (SLA) a nedoporučuje se používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tato veřejná Předběžná verze nabízí dva modely pro ukončení protokolu SSL:

- Můžete explicitně uvést certifikáty SSL, které jsou připojené k naslouchacímu procesu. Tento model je tradičním způsobem předat službě Application Gateway certifikáty SSL pro ukončení protokolu SSL.
- Volitelně můžete zadat odkaz na existující certifikát služby Key Vault nebo tajného klíče při vytváření naslouchací proces s povolenou komunikací HTTPS.

Integrace brány aplikací se službou Key Vault nabízí řadu výhod, včetně:

- Silnější zabezpečení, protože certifikáty SSL nezabývá přímo vývojovým týmem aplikace. Integrace umožňuje samostatné bezpečnostní tým na:
  * Nastavení služby application Gateway.
  * Řízení životního cyklu aplikací brány.
  * Udělení oprávnění pro vybrané aplikační brány pro přístup k certifikáty, které jsou uložené v trezoru klíčů.
- Podpora pro import existujících certifikátů do trezoru klíčů. Nebo vytvářet a spravovat nové certifikáty s některou z důvěryhodných partnerů služby Key Vault pomocí rozhraní API Key Vault.
- Podpora při automatickém prodlužování platnosti certifikátů, které jsou uložené v trezoru klíčů.

Služba Application Gateway aktuálně podporuje ověřené software jenom certifikáty. Modul hardwarového zabezpečení (HSM)-ověřené certifikáty nejsou podporovány. Po Application Gateway je konfigurován pro použití služby Key Vault certifikáty, její instance načtení certifikátu ze služby Key Vault a nainstalovat místně pro ukončení protokolu SSL. Instance také dotazovat služby Key Vault každých 24 hodin k načtení obnovené verzi certifikátu, pokud existuje. Pokud aktualizovaný certifikát není nalezen, je automaticky otočit certifikát SSL, který je aktuálně přidružený naslouchací proces HTTPS.

## <a name="how-integration-works"></a>Jak funguje integrace

Integrace brány aplikací se službou Key Vault vyžaduje proces konfigurace třech krocích:

1. **Vytvoření uživatelsky přiřazené identity spravované**

   Vytvořit nebo znovu použít existující uživatelsky přiřazené spravovaná identita, která Application Gateway používá k načtení certifikátů ze služby Key Vault za vás. Další informace najdete v tématu [co je spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md). Tento krok vytvoří novou identitu v tenantovi Azure Active Directory. Identita je důvěryhodný pro předplatné, které se používá k vytvoření identity.

1. **Konfigurace trezoru klíčů**

   Můžete pak buď naimportujte existující certifikát, nebo vytvořte novou v trezoru klíčů. Certifikát se používá aplikací spouštěných prostřednictvím application gateway. V tomto kroku můžete také použít tajný kód trezoru klíčů, který je uložen jako bez hesla, základní kódováním base64 souboru PFX. Doporučujeme používat typ certifikátu z důvodu možnosti automatické obnovení, který je k dispozici s objekty typu certifikátu ve službě key vault. Po vytvoření certifikátu nebo tajného kódu, definovat zásady přístupu ve službě key vault umožňuje identitě, kterou chcete udělit *získat* přístup k tajný kód.

1. **Nakonfigurujte aplikační bránu**

   Po dokončení předchozí dva kroky, můžete nastavit nebo upravit stávající aplikační brány pro použití spravované identity přiřazené uživateli. Můžete taky nakonfigurovat certifikát SSL pro naslouchací proces protokolu HTTP tak, aby odkazoval na úplný identifikátor URI služby Key Vault certifikát nebo ID tajného kódu.

   ![Certifikátů trezor klíčů](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Další postup

[Konfiguraci ukončení protokolu SSL s využitím certifikátů služby Key Vault pomocí Azure Powershellu](configure-keyvault-ps.md)

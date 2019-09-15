---
title: Ukončení protokolu SSL s certifikáty Azure Key Vault
description: Přečtěte si, jak můžete integrovat Azure Application Gateway s Key Vault pro certifikáty serveru, které jsou připojené k posluchačům s povoleným protokolem HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 725a9d67e6a6412fc48a4278b5a8a163272e5133
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000987"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Ukončení protokolu SSL s certifikáty Key Vault

[Azure Key Vault](../key-vault/key-vault-overview.md) je úložiště tajné databáze spravované platformou, které můžete použít k ochraně tajných klíčů, klíčů a certifikátů SSL. Azure Application Gateway podporuje integraci s Key Vault (ve verzi Public Preview) pro certifikáty serveru, které jsou připojené k naslouchacím procesům s povoleným protokolem HTTPS. Tato podpora je omezená na SKU v2 Application Gateway.

> [!IMPORTANT]
> Integrace Application Gateway s Key Vault je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb (SLA) a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tato veřejná verze Preview nabízí dva modely pro ukončení protokolu SSL:

- Můžete explicitně poskytnout certifikáty SSL připojené k naslouchacímu procesu. Tento model je tradiční způsob, jak předat certifikáty SSL Application Gateway pro ukončení protokolu SSL.
- Volitelně můžete zadat odkaz na existující Key Vault certifikát nebo tajný kód při vytváření naslouchacího procesu s povoleným protokolem HTTPS.

Application Gateway integrace s Key Vault nabízí spoustu výhod, včetně:

- Silnější zabezpečení, protože certifikáty SSL nejsou přímo zpracovávány týmem vývoje aplikace. Integrace umožňuje samostatnému bezpečnostnímu týmu:
  * Nastavte aplikační brány.
  * Řízení životního cyklu brány Application Gateway.
  * Udělte oprávnění vybraným aplikačním branám přístup k certifikátům uloženým v trezoru klíčů.
- Podpora importu existujících certifikátů do trezoru klíčů. Nebo můžete použít rozhraní Key Vault API k vytváření a správě nových certifikátů s některým z důvěryhodných partnerů Key Vault.
- Podpora automatického obnovování certifikátů uložených v trezoru klíčů.

Application Gateway aktuálně podporuje jenom certifikáty ověřované softwarem. Certifikáty ověřované modulem hardwarového zabezpečení (HSM) nejsou podporovány. Po nakonfigurování Application Gateway k používání certifikátů Key Vault jejich instance načtou certifikát z Key Vault a lokálně je nainstalují pro ukončení protokolu SSL. Instance také dotazují Key Vault ve 24hodinovém intervalu pro načtení obnovené verze certifikátu, pokud existuje. Pokud se najde aktualizovaný certifikát, automaticky se otočí certifikát SSL, který je aktuálně přidružený k naslouchacímu procesu HTTPS.

## <a name="how-integration-works"></a>Jak funguje integrace

Application Gateway integrace s Key Vault vyžaduje proces konfigurace se třemi kroky:

1. **Vytvoření spravované identity přiřazené uživatelem**

   Můžete vytvořit nebo znovu použít existující spravovanou identitu přiřazenou uživatelem, kterou Application Gateway používá k načtení certifikátů od Key Vault vaším jménem. Další informace najdete v tématu [co je spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md). Tento krok vytvoří novou identitu v tenantovi Azure Active Directory. Identita je důvěryhodná pro předplatné, které se používá k vytvoření identity.

1. **Konfigurace trezoru klíčů**

   Pak buď importujte existující certifikát, nebo vytvořte nový v trezoru klíčů. Certifikát budou používat aplikace, které běží přes Aplikační bránu. V tomto kroku můžete také použít tajný klíč trezoru klíčů, který je uložený jako soubor PFX s kódováním bez hesla, Base 64. Pro možnost autorecovery, která je k dispozici pro objekty typu certifikátu v trezoru klíčů, doporučujeme použít typ certifikátu. Po vytvoření certifikátu nebo tajného klíče v trezoru klíčů Definujte zásady přístupu, aby bylo možné udělení identity *získat* přístup k tajnému kódu.

1. **Konfigurace služby Application Gateway**

   Po dokončení obou předchozích kroků můžete nastavit nebo upravit existující Aplikační bránu tak, aby používala uživatelsky přiřazenou spravovanou identitu. Certifikát SSL naslouchacího procesu HTTP můžete také nakonfigurovat tak, aby odkazoval na úplný identifikátor URI Key Vault certifikátu nebo tajného ID.

   ![Certifikáty trezoru klíčů](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Další postup

[Konfigurace ukončení SSL pomocí Key Vault certifikátů pomocí Azure PowerShell](configure-keyvault-ps.md)

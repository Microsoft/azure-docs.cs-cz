---
title: Verze Azure Key Vault
description: Různé verze Azure Key Vault
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: mbaldwin
ms.openlocfilehash: 2396aac67a17352987d9d6e3b49535c19ffe8917
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792374"
---
# <a name="key-vault-versions"></a>Verze Key Vaultu

Tady je co je nový s Azure Key Vault. Nové funkce a vylepšení jsou také oznamovány na [Key Vaultm kanálu služby Azure Updates](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="june-2020"></a>Červen 2020

Azure Monitor pro Key Vault je nyní ve verzi Preview.  Azure Monitor poskytuje ucelený pohled na vaše trezory klíčů tím, že nabízí jednotný přehled o vašich Key Vaultch požadavcích, výkonu, selháních a latenci. Další informace najdete v tématu [Azure monitor pro Key Vault (Preview).](../../azure-monitor/insights/key-vault-insights-overview.md)

## <a name="may-2020"></a>Květen 2020

Key Vault "Přineste si vlastní klíč" (BYOK) je teď všeobecně dostupná. Přečtěte si část [Azure Key Vault BYOK Specification](../keys/byok-specification.md)a Naučte se [importovat klíče chráněné HSM do Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Březen 2020

Soukromé koncové body jsou nyní k dispozici ve verzi Preview. Služba privátního propojení Azure umožňuje přístup k Azure Key Vault a hostovaným zákaznickým a partnerským službám Azure přes privátní koncový bod ve vaší virtuální síti.  Naučte se [integrovat Key Vault s privátním propojením Azure](private-link-service.md).

## <a name="2019"></a>2019

- Vydání Azure Key Vault sad SDK nové generace. Příklady jejich použití najdete v tématu rychlý Start Azure Key Vault tajných kódů pro [Python](../secrets/quick-create-python.md), [.NET](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md)a [Node.js](../secrets/quick-create-node.md)
- Nové zásady Azure pro správu certifikátů trezoru klíčů. Přečtěte si [Azure Policy předdefinované definice pro Key Vault](../policy-reference.md).
- Rozšíření virtuálního počítače Azure Key Vault je teď všeobecně dostupné.  Viz [Key Vault rozšíření pro virtuální počítače pro Linux](../../virtual-machines/extensions/key-vault-linux.md) a [rozšíření virtuálního počítače Key Vault pro Windows](../../virtual-machines/extensions/key-vault-windows.md).
- Správa tajných kódů řízených událostmi pro Azure Key Vault nyní k dispozici v Azure Event Grid. Další informace najdete v tématu [schéma Event Grid pro události v Azure Key Vault] (.. /.. /Event-Grid/Event-Schema-Key-Vault.MD] a Naučte se, jak [přijímat a reagovat na oznámení o trezoru klíčů pomocí Azure Event Grid](event-grid-tutorial.md).

## <a name="2018"></a>2018

Nové funkce a integrace vydané tento rok:

- Integrace s Azure Functions. Příklad scénáře využití [Azure Functions](../../azure-functions/index.yml) pro operace trezoru klíčů najdete v tématu [Automatizace rotace tajného](../secrets/tutorial-rotation.md)klíče. 
- [Integrace s Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Díky tomu Azure Databricks nyní podporuje dva typy tajných oborů: zálohovaných Azure Key Vault a datacihly. Další informace najdete v tématu [Vytvoření oboru tajného klíče, který je Azure Key Vault zálohovaný](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope) .
- [Koncové body služby virtuální sítě pro Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Nové funkce vydané tento rok:

- Spravované klíče účtu úložiště. Funkce klíčů účtu úložiště přidala snazší integraci s Azure Storage. Další informace najdete v tématu Přehled – [Přehled klíčů spravovaného účtu úložiště](../secrets/overview-storage-keys.md).
- Obnovitelné odstranění. Funkce obnovitelného odstranění vylepšuje ochranu dat vašich trezorů klíčů a objektů trezoru klíčů. Další informace najdete v tématu Přehled – [Přehled o tichém odstranění](./soft-delete-overview.md).

## <a name="2015"></a>2015

Nové funkce vydané tento rok:
- Správa certifikátů. Přidáno do 26. září 2016 jako funkce pro GA verze 2015-06-01.

Obecná dostupnost (verze 2015-06-01) byla oznámena 24. června 2015. V této verzi byly provedeny následující změny: 
- Odstranění klíče – pole použít se odebralo.
- Načte informace o klíči – pole použít se odebralo.
- Import klíče do trezoru – pole použít se odebralo.
- Obnoví klíč – pole použít se odebralo.     
- Byl změněn "RSA_OAEP" na "RSA-výplně OAEP" pro algoritmy RSA. Přečtěte si [o klíčích, tajných klíčích a certifikátech](about-keys-secrets-certificates.md).    
 
Verze Second Preview (verze 2015-02-01-Preview) byla oznámena 20. dubna 2015. Další informace najdete v příspěvku na blogu [REST API Update](/archive/blogs/kv/rest-api-update) . Následující úkoly byly aktualizovány:
 
- Výpis klíčů v trezoru – Podpora stránkování je přidána do provozu.
- Vypíše verze operace přidané klíčovým seznamem verzí klíče.  
- Výpis tajných klíčů v trezoru – Podpora stránkování byla přidána.
- Vypíše verze operace s tajným kódem, aby vypisovat verze tajného kódu.  
- Všechny operace – do atributů se přidaly vytvořená a aktualizovaná časová razítka.  
- Vytvoření tajného klíče přidávaného obsahu k tajným klíčům.
- Vytvořte značky přidané klíčovým slovem jako volitelné informace.
- Vytvoření značek přidaných po tajnosti jako volitelných informací.
- Aktualizace značek přidaných klíčovým slovem jako volitelné informace
- Aktualizace značek přidaných tajnými znaky jako volitelné informace
- Změnila se maximální velikost tajných kódů z 10 K na 25 kB. Přečtěte si informace [o klíčích, tajných klíčích a certifikátech](about-keys-secrets-certificates.md).    
 
## <a name="2014"></a>2014
 
První verze Preview (verze 2014-12-08-Preview) byla oznámena 8. ledna 2015.  
 
## <a name="next-steps"></a>Další kroky

- [Informace o klíčích, tajných kódech a certifikátech](about-keys-secrets-certificates.md)
- [Klíče](../keys/index.yml)
- [Tajné kódy](../secrets/index.yml)
- [Certifikáty](../certificates/index.yml)
---
title: Přehled spravovaného modulu HSM v Azure – spravovaný modul HSM | Microsoft Docs
description: Spravovaný modul HSM Azure je cloudová služba, která chrání vaše kryptografické klíče pro cloudové aplikace.
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: overview
ms.custom: mvc
ms.date: 04/01/2021
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: a9df83535bf0ee7a6c484a7682392d2ad0ef59f7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815319"
---
# <a name="what-is-azure-key-vault-managed-hsm"></a>Co je Managed HSM služby Azure Key Vault?

Azure Key Vault spravovaný modul HSM je plně spravovaná cloudová služba s vysokou dostupností, která vyhovuje standardům, která vám umožní chránit kryptografické klíče pro vaše cloudové aplikace, a to pomocí ověřené HSM **úrovně 3 ve standardu FIPS 140-2** . Informace o cenách najdete v části spravované fondy HSM na [stránce s cenami Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). 

## <a name="why-use-managed-hsm"></a>Proč používat spravovaný modul HSM?

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>Plně spravovaná, vysoce dostupná, jediná tenant HSM jako služba

- **Plně spravovaná**: zřizování modulu HSM, konfigurace, opravy a údržba je služba zpracovávána. 
- **Vysoce dostupné a odolné vůči zóně** (kde jsou podporované zóny dostupnosti): každý cluster HSM se skládá z několika oddílů HSM, které se nacházejí v alespoň dvou zónách dostupnosti. Pokud se hardware nezdařil, oddíly členů clusteru HSM budou automaticky migrovány do funkčních uzlů.
- **Jeden tenant**: každá spravovaná instance HSM je vyhrazená pro jednoho zákazníka a skládá se z clusteru více oddílů HSM. Každý cluster HSM používá samostatnou doménu zabezpečení specifickou pro konkrétního zákazníka, která kryptograficky izoluje cluster HSM každého zákazníka.


### <a name="access-control-enhanced-data-protection--compliance"></a>Řízení přístupu, Rozšířená ochrana dat & dodržování předpisů

- **Centralizovaná správa klíčů**: na jednom místě spravujte kritické klíče s vysokými hodnotami napříč vaší organizací. S podrobnými oprávněními na klíč můžete řídit přístup ke každému klíči v zásadě nejnižší privilegovaný přístup.
- **Izolované řízení přístupu**: spravovaný modul pro řízení přístupu "místní služba HSM" umožňuje správcům clusteru HSM úplnou kontrolu nad hsmou, kterou dokonce skupina pro správu, předplatné nebo skupiny prostředků nemůže přepsat.
- **Ověření fips 140-2 Level 3 HSM**: Chraňte vaše data a splňovat požadavky na dodržování předpisů pomocí standardu FIPS ((federální Information Protection Standard)) 140-2 úrovně 3 ověřený HSM. Spravované HSM používají adaptéry PERC LiquidSecurity HSM.
- **Monitorování a audit**: plně integrovaná se službou Azure monitor. Získat kompletní protokoly o všech aktivitách prostřednictvím Azure Monitor. Pro analýzy a výstrahy použijte Azure Log Analytics.
- **Zasídlo dat**: spravovaný modul HSM neukládá ani nezpracovává zákaznická data mimo oblast, ve které zákazník nasadí instanci HSM.

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>Integrace s Azure a službami Microsoft PaaS/SaaS 

- Vygenerujte (nebo importujte s použitím [BYOK](hsm-protected-keys-byok.md)) klíče a využijte je k šifrování vašich dat v klidových službách Azure, jako jsou [Azure Storage](../../storage/common/customer-managed-keys-overview.md), [Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md)a [Azure Information Protection](/azure/information-protection/byok-price-restrictions).

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>Používá stejné rozhraní API a správy jako Key Vault

- K použití spravovaného HSM můžete snadno migrovat existující aplikace, které používají trezor (více tenantů).
- Používejte stejné vzory vývoje a nasazení aplikací pro všechny své aplikace bez ohledu na používané řešení pro správu klíčů: trezory víceklientské architektury nebo HSM spravované jedním klientem.

### <a name="import-keys-from-your-on-premise-hsms"></a>Import klíčů z místního HSM

- Vygenerujte klíče chráněné HSM v místním modulu HARDWAROVÉho zabezpečení a bezpečně je naimportujte do spravovaného HSM.

## <a name="next-steps"></a>Další kroky
- Informace o vytvoření a aktivaci spravovaného modulu HSM najdete v tématu [rychlý Start: zřízení a aktivace spravovaného HSM pomocí Azure CLI.](quick-create-cli.md)
- Podívejte se na [osvědčené postupy pomocí Azure Key Vault spravované HSM](best-practices.md) .

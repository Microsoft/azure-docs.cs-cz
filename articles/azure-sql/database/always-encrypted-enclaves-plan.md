---
title: Plán pro Intel SGX enclaves a ověření identity v Azure SQL Database
description: Naplánujte nasazení Always Encrypted s využitím zabezpečeného enclaves v Azure SQL Database.
keywords: šifrování dat, šifrování SQL, šifrování databáze, citlivá data, Always Encrypted, zabezpečení enclaves, SGX, ověřování identity
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: c41856c394166f2e3b8fd3bde794f0f294ef6af9
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253371"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>Plán pro Intel SGX enclaves a ověření identity v Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted se zabezpečeným enclaves pro Azure SQL Database je aktuálně ve **verzi Public Preview**.

[Always Encrypted se zabezpečeným enclaves](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves) v Azure SQL Database používá ENCLAVES [Intel SGX (software Guard Extensions)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) a vyžaduje [Microsoft Azure ověření identity](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation).

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>Plán pro Intel SGX v Azure SQL Database

Intel SGX je hardwarově důvěryhodná technologie spuštění prostředí. Intel SGX je k dispozici pro databáze, které používají [model Vcore](service-tiers-vcore.md) a generaci hardwaru [DC-Series](service-tiers-vcore.md?#dc-series) . Aby bylo zajištěno, že budete moci použít Always Encrypted s zabezpečeným enclaves ve vaší databázi, je třeba při vytváření databáze vybrat možnost generování hardwaru DC-Series, nebo můžete aktualizovat existující databázi, aby používala generování hardwaru DC-Series.

> [!NOTE]
> Intel SGX není k dispozici v jiných generacích hardwaru než DC-Series. Například Intel SGX není k dispozici pro hardware Gen5 a není k dispozici pro databáze používající [model DTU](service-tiers-dtu.md).

> [!IMPORTANT]
> Než nakonfigurujete generaci hardwaru DC-Series pro vaši databázi, zkontrolujte dostupnost místní dostupnosti řadičů DC-Series a ujistěte se, že rozumíte omezení výkonu. Podrobnosti najdete v tématu [DC-Series](service-tiers-vcore.md#dc-series).

## <a name="plan-for-attestation-in-azure-sql-database"></a>Plánování ověření identity v Azure SQL Database

[Ověřování identity Microsoft Azure](../../attestation/overview.md) (Preview) je řešení pro ověřování prostředí důvěryhodných spuštění (TEEs), včetně Intel SGX Enclaves ve službě Azure SQL Database s využitím generování hardwaru DC-Series.

Pokud chcete použít ověření Azure pro ověření identity Intel SGX enclaves v Azure SQL Database, musíte:

1. Vytvořte [poskytovatele ověření identity](../../attestation/basic-concepts.md#attestation-provider) a nakonfigurujte ho pomocí zásad ověřování identity. 

2. Udělte logickému serveru SQL Azure přístup k vytvořenému poskytovateli ověření identity.

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>Role a zodpovědnosti při konfiguraci SGX enclaves a ověření identity

Konfigurace prostředí pro podporu Intel SGX enclaves a ověření identity pro Always Encrypted v Azure SQL Database zahrnuje nastavení komponent různých typů: Microsoft Azure ověření identity, Azure SQL Database a aplikací, které aktivují ověřování enklávy. Konfigurace komponent každého typu se provádí uživateli za předpokladu, že jedna z následujících různých rolí:

- Správce ověření identity – vytvoří poskytovatele ověření identity v Microsoft Azure ověření identity, vytvoří autora zásady ověření, udělí přístup k logice serveru Azure SQL a nasdílí adresu URL ověření identity, která odkazuje na zásadu pro Správce aplikací.
- Správce Azure SQL Database – povolí SGX enclaves v databázích tak, že vyberou generaci hardwaru DC-Series a správce ověření identity s identitou logického serveru Azure SQL, který potřebuje přístup k poskytovateli ověření identity.
- Správce aplikací – nakonfiguruje aplikace s adresou URL ověření získanou od správce ověření identity.

V produkčních prostředích (zpracovávajících skutečná citlivá data) je důležité, aby vaše organizace při konfiguraci ověření identity dodržovala oddělení rolí, kde se každá z různých rolí předpokládá různí lidé. Zejména pokud je cílem nasazení Always Encrypted ve vaší organizaci omezit prostor pro útoky tím, že zajistíte Azure SQL Database správcům přístup k citlivým datům, Azure SQL Database by správci neměli řídit zásady ověření identity.

## <a name="next-steps"></a>Další kroky

- [Povolení Intel SGX pro Azure SQL Database](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>Viz také:

- [Kurz: Začínáme s Always Encrypted s využitím zabezpečených enclaves v Azure SQL Database](always-encrypted-enclaves-getting-started.md)
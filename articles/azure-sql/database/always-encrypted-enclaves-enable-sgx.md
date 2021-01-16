---
title: Povolit Intel SGX pro vaše Azure SQL Database
description: Přečtěte si, jak povolit Intel SGX pro Always Encrypted s zabezpečeným enclaves v Azure SQL Database tak, že vyberete generování hardwaru s podporou SGX.
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
ms.openlocfilehash: 4c77103dbb043ef9d6af9a4078b3e574ab5f953f
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253374"
---
# <a name="enable-intel-sgx-for-your-azure-sql-database"></a>Povolit Intel SGX pro vaše Azure SQL Database 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted se zabezpečeným enclaves pro Azure SQL Database je aktuálně ve **verzi Public Preview**.

[Always Encrypted se zabezpečeným enclaves](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves) v Azure SQL Database používá ENCLAVES [Intel SGX (software Guard Extensions)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) . Aby byla k dispozici Intel SGX, musí databáze používat [model Vcore](service-tiers-vcore.md) a generaci hardwaru [DC-Series](service-tiers-vcore.md#dc-series) .

Konfigurace hardwarového generování DC-Series, aby bylo možné povolit Intel SGX enclaves, je zodpovědností správce Azure SQL Database. [Při konfiguraci SGX enclaves a ověření identity se podívejte na role a zodpovědnosti](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

> [!NOTE]
> Intel SGX není k dispozici v jiných generacích hardwaru než DC-Series. Například Intel SGX není k dispozici pro hardware Gen5 a není k dispozici pro databáze používající [model DTU](service-tiers-dtu.md).

> [!IMPORTANT]
> Než nakonfigurujete generaci hardwaru DC-Series pro vaši databázi, zkontrolujte dostupnost místní dostupnosti řadičů DC-Series a ujistěte se, že rozumíte omezení výkonu. Další informace najdete v tématu [DC-Series](service-tiers-vcore.md#dc-series).

Podrobné pokyny pro konfiguraci nové nebo existující databáze pro použití konkrétního hardwarového generování najdete v tématu [Výběr hardwarového generování](service-tiers-vcore.md#selecting-a-hardware-generation).
   
## <a name="next-steps"></a>Další kroky

- [Konfigurace Azure Attestation pro váš server Azure SQL Database](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>Viz také:

- [Kurz: Začínáme s Always Encrypted s využitím zabezpečených enclaves v Azure SQL Database](always-encrypted-enclaves-getting-started.md)
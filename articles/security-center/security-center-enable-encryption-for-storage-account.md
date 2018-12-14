---
title: Povolit šifrování pro účet úložiště ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje, jak implementovat doporučení Azure Security Center **povolit šifrování pro účet služby Azure Storage**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 727e0cc7f0dac18a6ac9e97b3c6edae3ba8f62d6
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338018"
---
# <a name="enable-encryption-for-azure-storage-account-in-azure-security-center"></a>Povolit šifrování pro účet úložiště Azure ve službě Azure Security Center
Azure Security Center může doporučit, povolit šifrování služby Azure Storage pro neaktivní uložená data.

Šifrování služby Storage (SSE) funguje tak, že šifrování dat při zápisu do úložiště Azure a dešifrování dat před načítání.  SSE je momentálně dostupná jenom pro službu Azure Blob service a lze použít pro objekty BLOB bloku, objekty BLOB stránky a doplňovací objekty BLOB.  Další informace najdete v tématu [šifrování služby Storage pro neaktivní uložená data](../storage/common/storage-service-encryption.md).


> [!Note]
> Po povolení šifrování, je šifrována pouze nová data. Všechny existující objekty BLOB v účtu úložiště zůstanou nezašifrované. K šifrování existujících objektů BLOB, najdete v článku [Storage Service Encryption nejčastější dotazy k](../storage/common/storage-service-encryption.md#faq-for-storage-service-encryption).
>
>

Šifrování služby Storage je podporována pouze pro účty úložiště Resource Manageru. Účty úložiště Classic se aktuálně nepodporují. Klasické modely nasazení a modely nasazení Resource Manageru najdete v tématu [modelech nasazení Azure](../azure-classic-rm.md).

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Tento dokument není podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V **doporučení** okně vyberte **povolit šifrování pro účet služby Azure Storage**.
   ![Povolení šifrování pro účet úložiště][1]
2. **Povolit šifrování úložiště** se otevře okno. Toto okno obsahuje seznam účtů úložiště Azure, kde je zakázané šifrování úložiště. V tomto příkladu vybereme **storageacct1**.
   ![Povolit šifrování úložiště][2]
3. **Šifrování** okno **storageacct1** otevře. Vyberte **povolené**.
   ![Okno šifrování][3]
4. Vyberte **Uložit**.

Nyní jste povolili šifrování úložiště pro **storageacct1**.


## <a name="see-also"></a>Další informace najdete v tématech
Tento dokument vám ukázali, jak implementovat doporučení služby Security Center "Povolit šifrování pro účet služby Azure Storage." Další informace o šifrování služby Azure Storage, naleznete v následujících tématech:

* [Šifrování služby Azure Storage pro neaktivní uložená Data](../storage/common/storage-service-encryption.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení ve službě Azure Security Center](tutorial-security-policy.md) – zjistěte, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak můžete monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat a reagovat na výstrahy zabezpečení.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – přečtěte si blog příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-enable-encryption-for-storage-account/enable-encryption-for-storage-account.png
[2]: ./media/security-center-enable-encryption-for-storage-account/enable-storage-encryption.png
[3]: ./media/security-center-enable-encryption-for-storage-account/encryption-blade.png

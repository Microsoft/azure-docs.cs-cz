---
title: Zabezpečení aplikací PaaS použití služby Azure Storage | Microsoft Docs
description: " Další informace o zabezpečení Azure Storage osvědčené postupy pro zabezpečení vašich PaaS webové a mobilní aplikace. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/20/2018
ms.author: TomShinder
ms.openlocfilehash: ffc04973a003c65f52f3387292f11fede65edce3
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295291"
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-storage"></a>Zabezpečení PaaS webové a mobilní aplikace pomocí Azure Storage

V tomto článku probereme kolekce Azure Storage osvědčené postupy zabezpečení pro zabezpečení vašich PaaS webové a mobilní aplikace. Tyto doporučené postupy jsou odvozeny od našich zkušeností s Azure a prostředí zákazníků jako sami.

[Průvodce zabezpečením Azure Storage](../storage/common/storage-security-guide.md) je skvělým zdrojem pro podrobné informace o Azure Storage a zabezpečení.  Tento článek zaměřuje na vysoké úrovni některé koncepty, nalezených v příručce zabezpečení a odkazy na v příručce zabezpečení, jakož i jiných zdrojů, další informace.

## <a name="azure-storage"></a>Azure Storage

Azure umožňuje nasadit a používat úložiště způsoby snadno dosažitelné místní. S Azure storage dosáhnout vysoké úrovně, škálovatelnost a dostupnost s relativně malým množstvím úsilí. Úložiště Azure nejen je základem pro systém Windows a Linux Azure Virtual Machines, může také podporovat velkých distribuovaných aplikací.

Úložiště Azure nabízí tyto čtyři služby: úložiště Blob, úložiště Table, úložiště Queue, a úložiště File. Další informace najdete v tématu [Úvod do Microsoft Azure Storage](../storage/storage-introduction.md).

## <a name="best-practices"></a>Osvědčené postupy

Tento článek řeší následující osvědčené postupy:

- NAP:
   - Sdílené přístupové podpisy (SAS)
   - Řízení přístupu na základě role (RBAC)

- Šifrování úložiště:
   - Šifrování na straně klienta pro cenných dat
   - Storage Service Encryption

## <a name="access-protection"></a>NAP

### <a name="use-shared-access-signature-instead-of-a-storage-account-key"></a>Použití sdíleného přístupového podpisu místo klíče účtu úložiště.

V řešení IaaS, obvykle systémem Windows Server nebo Linux virtuální počítače chráněné soubory z zpřístupnění a zneužitím ohrožení pomocí mechanismy řízení přístupu. V systému Windows byste použili [seznamy řízení (ACL) přístupu](../virtual-network/virtual-networks-acl.md) a v systému Linux, pravděpodobně použijete [chmod](https://en.wikipedia.org/wiki/Chmod). V podstatě jde přesně jaká by provést, pokud byly dnes Ochrana souborů na server v datovém centru.

PaaS se liší. Mezi nejběžnější způsoby k ukládání souborů v Microsoft Azure je použití [úložiště objektů Azure Blob](../storage/storage-dotnet-how-to-use-blobs.md). Rozdíl mezi úložiště objektů Blob a jiného úložiště souborů je vstupně-výstupní soubor a metody ochrany, které jsou součástí souboru vstupně-výstupních operací.

Řízení přístupu je velmi důležité. Můžete řídit přístup k úložišti Azure, systém vygeneruje dva klíče účtu úložiště 512 bitů (SAKs) Pokud jste [vytvořit účet úložiště](../storage/common/storage-create-storage-account.md). Úroveň klíče redundance umožňuje zabránit přerušení služby během běžné střídání klíče.

Přístupové klíče k úložišti jsou tajné klíče s vysokou prioritou a by měly být jenom přístup osobám odpovědným za řízení přístupu úložiště. Pokud nikdo získat přístup k těmto klíčům, můžou bude mít úplnou kontrolu nad úložiště a nahradit, odstranit nebo přidat soubory do úložiště. To zahrnuje malwaru a jiné typy obsahu, který může potenciálně ohrozit vaše organizace nebo vašich zákazníků.

Stále potřebujete způsob, jak poskytnout přístup k objektům v úložišti. K poskytování přístupu podrobnější můžete využít výhod [sdíleného přístupového podpisu](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS). SAS umožňuje sdílet určitých objektů v úložišti pro předem definovaný časový interval a se specifickými oprávněními. Sdíleného přístupového podpisu můžete zadat:

- Interval, za které je platný, včetně počáteční čas a čas vypršení platnosti SAS.
- Oprávnění udělená pomocí SAS. SAS na objekt blob může například udělit čtení uživatele a oprávnění k zápisu k tomuto objektu blob ale oprávnění k odstranění.
- Volitelné IP adresu nebo rozsah IP adres, ze kterých Azure Storage přijímá SAS. Například můžete určit rozsah IP adres, které patří do vaší organizace. To poskytuje jiné míře zabezpečení pro vaše SAS.
- Protokol, přes který Azure Storage přijímá SAS. Tento volitelný parametr můžete omezit přístup ke klientům pomocí protokolu HTTPS.

SAS můžete sdílet obsah způsob, jakým chcete sdílet bez nutnosti poskytnutí rychle klíče účtu úložiště. Vždy pomocí SAS v aplikaci je zabezpečení způsob, jak sdílet svým prostředkům úložiště bez kompromisů klíče účtu úložiště.

Další informace najdete v tématu [pomocí sdílené přístupové podpisy](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS). Další informace o potenciální rizika a doporučení pro zmírnění najdete v tématu [osvědčených postupů při použití SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="use-role-based-access-control"></a>Pomocí řízení přístupu na základě rolí

Výše jsme probrali udělit omezený přístup k objektům v účtu úložiště pro ostatní klienty bez vystavení klíč účtu úložiště účet pomocí sdíleného přístupového podpisu (SAS). Někdy rizika spojená s konkrétní operaci u vašeho účtu úložiště převažují nad přínosy SAS. Někdy je snazší správa přístupu k jiným způsobem.

Jiný způsob, jak spravovat přístup je použití [řízení přístupu](../role-based-access-control/overview.md) (RBAC). V RBAC zaměřit se na poskytnutí zaměstnanci přesný oprávnění, které potřebují, na základě potřeba znát a principy zabezpečení nejnižší oprávnění. Účet, který se útočníci můžou zpřístupnit příliš mnoho oprávnění. Příliš málo oprávnění znamená, že zaměstnanci nelze práci efektivně. AZURE pomůže vyřešit tento problém tak, že nabídka vyladění správy přístupu pro Azure. To je nutné pro organizace, které chcete vynutit zásady zabezpečení pro přístup k datům.

Můžete využít předdefinované role RBAC v Azure přiřadit oprávnění pro uživatele. Zvažte použití Přispěvatel účet úložiště pro operátorům cloudu, kteří potřebují spravovat účty úložiště a role Classic Přispěvatel účtu úložiště ke správě klasické účty úložiště. Pro cloudové operátory, které je potřeba spravovat virtuální počítače, ale není virtuální sítě nebo úložiště účet ke kterému jsou připojeny, zvažte přidání je k roli Přispěvatel virtuálních počítačů.

Organizace, které nebudou vynucovat řízení přístupu dat s využitím funkcí, jako je RBAC může být poskytnutí další oprávnění, než je nezbytné pro své uživatele. To může vést k ohrožení zabezpečení dat tím, že někteří uživatelé přístup k datům, které by neměly mít na prvním místě.

Další informace o RBAC najdete v tématu:

- [Řízení přístupu Azure na základě rolí](../role-based-access-control/role-assignments-portal.md)
- [Předdefinované role pro řízení přístupu Azure na základě rolí](../role-based-access-control/built-in-roles.md)
- [Průvodce zabezpečením služby Azure Storage](../storage/common/storage-security-guide.md) podrobnosti o tom, jak zabezpečit váš účet úložiště s RBAC

## <a name="storage-encryption"></a>Šifrování úložiště

### <a name="use-client-side-encryption-for-high-value-data"></a>Používat šifrování na straně klienta dat vysoké hodnoty

Šifrování na straně klienta umožňuje prostřednictvím kódu programu šifrování přenášených dat před nahráním do úložiště Azure a prostřednictvím kódu programu dešifrování dat při načítání z úložiště.  To zajišťuje šifrování dat během přenosu, ale také poskytuje šifrování dat v klidovém stavu.  Šifrování na straně klienta je nejbezpečnější metodou šifrování dat, ale vyžaduje provedení programové změny do vaší aplikace a put procesy správy klíčů na místě.

Šifrování na straně klienta také umožňuje mít výhradní kontrolu nad šifrovacích klíčů.  Můžete vygenerovat a spravovat vlastní šifrovací klíče.  Šifrování na straně klienta používá metodu obálky, kde knihovnu klienta služby Azure storage vygeneruje obsahu šifrovací klíč (CEK) pak zabalený (šifrované) pomocí klíčů šifrovacího klíče (KEK). Klíče KEK je identifikovaná identifikátorem klíče a může být pár asymetrických klíčů nebo symetrického klíče a mohou být spravovaný místně nebo uloženy v [Azure Key Vault](../key-vault/key-vault-whatis.md).

Šifrování na straně klienta jsou součástí jazyce Java a knihovny klienta úložiště .NET.  V tématu [šifrování na straně klienta a Azure Key Vault pro Microsoft Azure Storage](../storage/storage-client-side-encryption.md) informace o šifrování dat v rámci klientské aplikace a generování a správu šifrovacích klíčů.

### <a name="storage-service-encryption"></a>Storage Service Encryption

Když [šifrování služby úložiště](../storage/storage-service-encryption.md) pro úložiště souborů je povoleno, data se šifrují automaticky pomocí šifrování AES-256. Microsoft zpracovává všechny šifrování, dešifrování a správu klíčů. Tato funkce je dostupná pro typy LRS a GRS redundance.

## <a name="next-steps"></a>Další postup

Tento článek seznámili kolekce Azure Storage osvědčené postupy zabezpečení pro zabezpečení vašich PaaS webové a mobilní aplikace. Další informace o zabezpečení vašich PaaS nasazení najdete v tématu:

- [Zabezpečení nasazení PaaS](security-paas-deployments.md)
- [Zabezpečení PaaS webové a mobilní aplikace pomocí Azure App Services](security-paas-applications-using-app-services.md)
- [Zabezpečení databáze PaaS v Azure](security-paas-applications-using-sql.md)

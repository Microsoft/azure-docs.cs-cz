---
title: Zabezpečení aplikací PaaS pomocí Azure Storage | Microsoft Docs
description: Přečtěte si o Azure Storage osvědčených postupech zabezpečení pro zabezpečení webových a mobilních aplikací PaaS.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: tomsh
ms.openlocfilehash: 12cce0eac3827046ae9171f3dd5696ae5905c802
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093987"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Osvědčené postupy pro zabezpečení webových a mobilních aplikací PaaS pomocí Azure Storage
V tomto článku se zabýváme kolekcí Azure Storage osvědčených postupů zabezpečení pro zabezpečení vašich webových a mobilních aplikací PaaS (Platform as a Service). Tyto osvědčené postupy se odvozují z našich zkušeností s Azure a zkušeností zákazníků, jako je sami.

Azure umožňuje nasazení a používání úložiště způsobem, který není možné snadno dosáhnout místně. S Azure Storage můžete dosáhnout vysoké úrovně škálovatelnosti a dostupnosti s poměrně malým úsilím. Pouze Azure Storage Foundation pro Windows a Linux Azure Virtual Machines, může také podporovat velké distribuované aplikace.

Azure Storage poskytuje následující čtyři služby: úložiště objektů blob, úložiště tabulek, Queue Storage a File Storage. Další informace najdete v tématu [Úvod do Microsoft Azure Storage](../../storage/common/storage-introduction.md).

[Azure Storage Příručka zabezpečení](../../storage/blobs/security-recommendations.md) je skvělým zdrojem pro podrobné informace o Azure Storage a zabezpečení. Tento článek s osvědčenými postupy řeší na nejvyšší úrovni některé koncepty, které najdete v příručce zabezpečení, a odkazy na příručku zabezpečení a také na další zdroje, kde najdete další informace.

Tento článek popisuje následující osvědčené postupy:

- Sdílené přístupové podpisy (SAS)
- Řízení přístupu na základě role Azure (Azure RBAC)
- Šifrování na straně klienta pro data s vysokou hodnotou
- Šifrování služby Storage


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Použití sdíleného přístupového podpisu místo klíče účtu úložiště
Řízení přístupu je důležité. Při vytváření účtu úložiště vám Azure vygeneruje 2 512 klíčů účtu úložiště (SAKs), které vám pomůžou řídit přístup k Azure Storage. Úroveň redundance klíčů umožňuje vyhnout se přerušením služeb během pravidelného střídání klíčů. 

Přístupové klíče k úložišti mají tajné klíče s vysokou prioritou a měly by být dostupné jenom pro ty, kteří zodpovídají za řízení přístupu k úložišti. Pokud k těmto klíčům získají přístup špatné osoby, budou mít úplnou kontrolu nad úložištěm a můžou nahradit, odstranit nebo přidat soubory do úložiště. To zahrnuje malware a další typy obsahu, které mohou potenciálně ohrozit vaši organizaci nebo vaše zákazníky.

Stále potřebujete způsob, jak poskytnout přístup k objektům v úložišti. Pro zajištění přesnější přístupu můžete využít výhod sdíleného přístupového podpisu (SAS). SAS umožňuje sdílet konkrétní objekty v úložišti pro předem definovaný časový interval a s konkrétními oprávněními. Sdílený přístupový podpis umožňuje definovat:

- Interval, po který je SAS platný, včetně času zahájení a času vypršení platnosti.
- Oprávnění udělená SAS. Například SAS na objektu BLOB může udělit uživateli oprávnění ke čtení a zápisu pro daný objekt blob, ale ne oprávnění k odstranění.
- Volitelná IP adresa nebo rozsah IP adres, ze kterých Azure Storage akceptuje SAS. Můžete například zadat rozsah IP adres, které patří vaší organizaci. Tím se zajistí další míra zabezpečení SAS.
- Protokol, přes který Azure Storage akceptuje SAS. Pomocí tohoto volitelného parametru můžete omezit přístup k klientům pomocí protokolu HTTPS.

Pomocí SAS můžete sdílet obsah tak, jak ho chcete sdílet, aniž byste museli své klíče účtu úložiště dál vydávat. Používání SAS ve vaší aplikaci je vždy zabezpečený způsob, jak sdílet prostředky úložiště bez narušení klíčů účtu úložiště.

Další informace o sdíleném přístupovém podpisu najdete v tématu [použití sdílených přístupových](../../storage/common/storage-sas-overview.md)podpisů. 

## <a name="use-azure-role-based-access-control"></a>Použití řízení přístupu na základě role v Azure
Dalším způsobem, jak spravovat přístup, je použití [řízení přístupu na základě role Azure (RBAC)](../../role-based-access-control/overview.md). Pomocí služby Azure RBAC se zaměříte na to, aby zaměstnanci měli přesně potřebná oprávnění, a to na základě nutnosti znát a nejnižší principy zabezpečení oprávnění. Příliš mnoho oprávnění může zpřístupnit účet pro útočníky. Příliš málo oprávnění znamená, že zaměstnanci nemůžou svou práci efektivně dělat. Služba Azure RBAC pomáhá vyřešit tento problém tím, že nabízí jemně odstupňovanou správu přístupu pro Azure. To je nezbytné pro organizace, které chtějí vyhovět zásadám zabezpečení pro přístup k datům.

K přiřazení oprávnění uživatelům můžete použít předdefinované role Azure v Azure. Například použijte Přispěvatel účtu úložiště pro operátory cloudu, kteří potřebují spravovat účty úložiště a roli Přispěvatel klasických účtů úložiště pro správu klasických účtů úložiště. Pro operátory cloudu, které potřebují spravovat virtuální počítače, ale ne virtuální síť nebo účet úložiště, ke kterým jsou připojené, je můžete přidat do role Přispěvatel virtuálních počítačů.

Organizace, které vynutily řízení přístupu k datům pomocí možností, jako je Azure RBAC, můžou udělit větší oprávnění, než je potřeba pro své uživatele. To může vést k ohrožení dat tím, že některým uživatelům umožní přístup k datům, která by neměla mít na prvním místě.

Další informace o službě Azure RBAC najdete v těchto tématech:

- [Přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Předdefinované role Azure](../../role-based-access-control/built-in-roles.md)
- [Průvodce zabezpečením Azure Storage](../../storage/blobs/security-recommendations.md) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Použití šifrování na straně klienta pro data s vysokou hodnotou
Šifrování na straně klienta umožňuje programově šifrovat data při přenosu před odesláním do Azure Storage a programově dešifrovat data při jejich načítání. Díky tomu je zajištěno šifrování přenášených dat, ale zároveň je zajištěno šifrování v klidovém režimu. Šifrování na straně klienta představuje nejbezpečnější metodu šifrování dat, ale vyžaduje, abyste v rámci aplikace napravili programové změny a zavedli procesy správy klíčů na místo.

Šifrování na straně klienta také umožňuje mít jedinou kontrolu nad šifrovacími klíči. Můžete generovat a spravovat vlastní šifrovací klíče. Používá techniku obálky, kde Klientská knihovna služby Azure Storage generuje šifrovací klíč obsahu (CEK), který je pak zabalené (šifrovaný) pomocí klíče šifrování klíčů (KEK). KEK je identifikován identifikátorem klíče a může se jednat o asymetrický klíč nebo symetrický klíč a lze ho spravovat místně nebo uložit v [Azure Key Vault](../../key-vault/general/overview.md).

Šifrování na straně klienta je integrováno do klientských knihoven Java a .NET Storage. Další informace o šifrování dat v klientských aplikacích a vytváření a správě vlastních šifrovacích klíčů najdete v tématu [šifrování na straně klienta a Azure Key Vault pro Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md) .

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Povolit Šifrování služby Storage pro neaktivní neaktivní data
Když je povolená [šifrování služby Storage](../../storage/common/storage-service-encryption.md) pro úložiště souborů, data se šifrují automaticky pomocí šifrování AES-256. Microsoft zpracovává veškerá šifrování, dešifrování a správu klíčů. Tato funkce je k dispozici pro typy redundance LRS a GRS.

## <a name="next-steps"></a>Další kroky

Tento článek vás zavedl do kolekce Azure Storage osvědčené postupy zabezpečení pro zabezpečení webových a mobilních aplikací PaaS. Další informace o zabezpečení nasazení PaaS najdete v těchto tématech:

- [Zabezpečení nasazení PaaS](paas-deployments.md)
- [Zabezpečení webových a mobilních aplikací PaaS pomocí Azure App Services](paas-applications-using-app-services.md)
- [Zabezpečení databází PaaS v Azure](paas-applications-using-sql.md)
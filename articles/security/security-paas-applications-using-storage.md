---
title: Zabezpečení aplikací PaaS pomocí služby Azure Storage | Dokumentace Microsoftu
description: Další informace o zabezpečení služby Azure Storage osvědčené postupy pro zabezpečení vašich webových a mobilních aplikací PaaS.
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
ms.date: 09/28/2018
ms.author: TomShinder
ms.openlocfilehash: ac01aaca8c147b1f474b59ac57424f5cdc5f8a8d
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451863"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Osvědčené postupy pro zabezpečení webových a mobilních aplikací pomocí služby Azure Storage PaaS
V tomto článku se podíváme na kolekci osvědčené postupy zabezpečení služby Azure Storage pro zabezpečení platforma jako služba (PaaS) webových a mobilních aplikací. Tyto osvědčené postupy jsou odvozeny z našich zkušenostech s Azure a prostředí zákazníků, jako sami.

Azure umožňuje nasadit a používat úložiště způsoby snadno dosáhnout místní. S Azure storage můžete oslovit přináší vysokou úroveň škálovatelnosti a dostupnosti s relativně málo úsilí. Nejenže Azure Storage je základem pro Windows a virtuální počítače Azure s Linuxem, může také podporovat velký distribuované aplikace.

Služba Azure Storage poskytuje následující čtyři služby: Blob storage, Table storage, Queue storage a File storage. Další informace najdete v tématu [Úvod do Microsoft Azure Storage](../storage/storage-introduction.md).

[Průvodci zabezpečením Azure Storage](../storage/common/storage-security-guide.md) je skvělým zdrojem pro podrobné informace o Azure Storage a zabezpečení. Tento článek o osvědčených postupech řeší na vysoké úrovni některé koncepty v Průvodci zabezpečením a odkazy na Průvodce zabezpečení, jakož i jiných zdrojů, další informace.

Tento článek se zabývá následující osvědčené postupy:

- Sdílené přístupové podpisy (SAS)
- Řízení přístupu na základě role (RBAC)
- Šifrování na straně klienta pro cenných dat
- Storage Service Encryption


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Místo klíče účtu úložiště pomocí sdíleného přístupového podpisu
Řízení přístupu je velmi důležité. Můžete řídit přístup ke službě Azure Storage, Azure vygeneruje dva klíče účtu úložiště 512 bitů (SAKs) při vytváření účtu úložiště. Úroveň redundance klíče umožňuje vyhnout se přerušením služeb během běžné obměny klíče. 

Přístupové klíče k úložišti s vysokou prioritou některým tajným kódům a by mělo mít pouze přístup osobám odpovědným za řízení přístupu úložiště. Pokud nesprávní lidé získat přístup k těmto klíčům, jsou bude naprostou kontrolu nad úložiště a může nahradit, odstranit nebo přidejte soubory do úložiště. To zahrnuje malware a další typy obsahu, který může potenciálně ohrozit vaši organizaci i zákazníky.

Stále musíte poskytnout přístup k objektům ve službě storage. A zajistit tak podrobnější přístup můžete využít sdílený přístupový podpis (SAS). SAS umožňuje sdílení konkrétních objektů v úložišti pro předem definovaný časový interval a se specifickými oprávněními. Sdílený přístupový podpis můžete zadat:

- Interval, přes který je platný, včetně počáteční čas a čas vypršení platnosti SAS.
- Oprávnění udělená SAS. SAS objektu BLOB může například udělit čtení uživatelů a oprávnění k zápisu do tohoto objektu blob ale oprávnění k odstranění.
- Volitelná adresa IP nebo rozsah IP adres, ze kterých Azure Storage přijímá SAS. Například můžete určit rozsah IP adres, které patří vaší organizaci. To poskytuje další míry zabezpečení pro vaše SAS.
- Protokol nad tím, které služby Azure Storage přijímá SAS. Tento nepovinný parametr slouží k omezení přístupu ke klientům pomocí protokolu HTTPS.

SAS můžete sdílet obsah tak, jak chcete sdílet bez ztráty okamžitě své klíče účtu úložiště. Vždy pomocí SAS ve vaší aplikaci je zabezpečený způsob, jak sdílet prostředky úložiště, aniž byste ohrozili své klíče účtu úložiště.

Další informace o sdílených přístupových podpisů najdete v tématu [použití sdílených přístupových podpisů](../storage/common/storage-dotnet-shared-access-signature-part-1.md). 

## <a name="use-role-based-access-control"></a>Použití řízení přístupu na základě rolí
Dalším způsobem, jak spravovat přístup je určený [řízení přístupu na základě rolí](../role-based-access-control/overview.md) (RBAC). Pomocí RBAC, zaměřit se na konkrétní oprávnění, které potřebují, poskytuje zaměstnancům na základě potřeba znát a principů zabezpečení nejnižších oprávnění. Příliš mnoho oprávnění, mohou vystavit účet tak, aby útočníci. Příliš málo oprávnění znamená, že zaměstnanci nelze práci efektivně. AZURE pomůže tento problém vyřešit tím, že nabízí propracovanou správu přístupu pro Azure. To je nezbytné pro organizace, které chcete vynutit zásady zabezpečení pro přístup k datům.

Předdefinované role RBAC v Azure můžete přiřadit oprávnění pro uživatele. Přispěvatel účtů úložiště můžete například použijte pro operátorům cloudu, které je potřeba spravovat účty úložiště a role Přispěvatel účtů klasického úložiště ke správě klasických účtů úložiště. Pro cloud operátory, které je potřeba spravovat virtuální počítače, ale není virtuální síť nebo účet úložiště, ke kterému jsou připojené můžete je přidat k roli Přispěvatel virtuálních počítačů.

Organizace, které se nebudou vynucovat řízení přístupu dat pomocí funkcí, jako je RBAC může poskytuje více oprávnění než je nezbytné pro své uživatele. To může vést k ohrožení dat tím, že někteří uživatelé přístup k datům, které by neměly mít na prvním místě.

Další informace o RBAC najdete v tématu:

- [Správa přístupu pomocí RBAC a portálu Azure Portal](../role-based-access-control/role-assignments-portal.md)
- [Předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md)
- [Příručka zabezpečení Azure Storage](../storage/common/storage-security-guide.md) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Použít šifrování na straně klienta pro data s vysokou hodnotou
Šifrování na straně klienta můžete programově zašifrovat data přenášená před nahráním do služby Azure Storage a při jeho načítání prostřednictvím kódu programu dešifrovat data. To poskytuje šifrování přenášených dat, ale také zajišťuje šifrování pro neaktivní uložená data. Šifrování na straně klienta je nejbezpečnější metodu šifrování dat ale můžete změnit prostřednictvím kódu programu do vaší aplikace a procesy správy klíčů na místě vyžaduje.

Šifrování na straně klienta umožňuje mít výhradní kontrolu nad šifrovací klíče. Můžete vygenerovat a spravovat vlastní šifrovací klíče. Ji používá metodu obálky, kde klientskou knihovnu pro úložiště Azure vygeneruje obsahu šifrovací klíč (CEK), která je zabalena pak (zašifrovaný) pomocí šifrovací klíč klíče (KEK). Klíče KEK je identifikovaná identifikátorem klíče a lze asymetrický pár klíčů nebo symetrický klíč a může být spravovaný místně nebo uložené v [Azure Key Vault](../key-vault/key-vault-whatis.md).

Šifrování na straně klienta je integrované do jazyka Java a klientské knihovny úložiště .NET. Zobrazit [šifrování na straně klienta a služby Azure Key Vault pro Microsoft Azure Storage](../storage/storage-client-side-encryption.md) informace o šifrování dat v rámci klientské aplikace a generuje a spravuje vlastní šifrovací klíče.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Povolení šifrování služby Storage pro neaktivní uložená data
Když [šifrování služby Storage](../storage/storage-service-encryption.md) pro File storage je povoleno, data se zašifrují automaticky pomocí šifrování AES-256. Společnost Microsoft zpracovává šifrování, dešifrování a správu klíčů. Tato funkce je dostupná pro typy redundance LRS a GRS.

## <a name="next-steps"></a>Další postup

V tomto článku jste se seznámili s kolekce osvědčené postupy zabezpečení služby Azure Storage pro zabezpečení vašich webových a mobilních aplikací PaaS. Další informace o zabezpečení nasazení PaaS najdete v tématu:

- [Zabezpečení nasazení PaaS](security-paas-deployments.md)
- [Zabezpečení PaaS webových a mobilních aplikací pomocí služby Azure App Services](security-paas-applications-using-app-services.md)
- [Zabezpečení databáze PaaS v Azure](security-paas-applications-using-sql.md)

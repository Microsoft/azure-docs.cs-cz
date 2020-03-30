---
title: Zabezpečení aplikací PaaS pomocí Služby Azure Storage | Dokumenty společnosti Microsoft
description: Přečtěte si o doporučených postupech zabezpečení Azure Storage pro zabezpečení webových a mobilních aplikací PaaS.
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
ms.openlocfilehash: 675e10101d01d831aad7652c70cbfcf320085a3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70999165"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Doporučené postupy pro zabezpečení webových a mobilních aplikací PaaS pomocí Služby Azure Storage
V tomto článku se zabýváme kolekcí doporučených postupů zabezpečení Azure Storage pro zabezpečení webových a mobilních aplikací paas (platformy jako služby). Tyto osvědčené postupy jsou odvozeny z našich zkušeností s Azure a zkušeností zákazníků, jako jste vy.

Azure umožňuje nasazovat a používat úložiště způsoby, které nejsou snadno dosažitelné místně. Díky úložišti Azure můžete dosáhnout vysoké úrovně škálovatelnosti a dostupnosti s relativně malým úsilím. Azure Storage je nejen základem pro virtuální počítače s Windows a LinuxAzure, ale taky může podporovat velké distribuované aplikace.

Azure Storage poskytuje následující čtyři služby: úložiště objektů blob, úložiště tabulek, úložiště front a úložiště souborů. Další informace najdete [v tématu Úvod do úložiště Microsoft Azure](/azure/storage/common/storage-introduction).

[Průvodce zabezpečením azure storage](/azure/storage/common/storage-security-guide) je skvělý zdroj informací o Azure Storage a zabezpečení. Tento článek osvědčených postupů se zabývá na vysoké úrovni některé koncepty nalezené v příručce zabezpečení a odkazy na průvodce zabezpečením, stejně jako jiné zdroje, pro další informace.

Tento článek se zabývá následujícími doporučenými postupy:

- Sdílené přístupové podpisy (SAS)
- Řízení přístupu na základě role (RBAC)
- Šifrování na straně klienta pro data s vysokou hodnotou
- Šifrování služby Storage


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Použití sdíleného přístupového podpisu místo klíče účtu úložiště
Řízení přístupu je důležité. Abychom vám pomohli řídit přístup k Azure Storage, Azure generuje dva klíče účtu 512bitového úložiště (SAKs) při vytváření účtu úložiště. Úroveň redundance klíče umožňuje vyhnout se přerušení služby během rutinního střídání klíčů. 

Přístupové klíče úložiště jsou tajné klíče s vysokou prioritou a měly by být přístupné pouze osobám odpovědným za řízení přístupu k úložišti. Pokud k těmto klíčům získají přístup nezvladatní lidé, budou mít úplnou kontrolu nad úložištěm a mohou do úložiště nahradit, odstranit nebo přidat soubory. To zahrnuje malware a další typy obsahu, které mohou potenciálně ohrozit vaši organizaci nebo vaše zákazníky.

Stále potřebujete způsob, jak poskytnout přístup k objektům v úložišti. Chcete-li poskytnout podrobnější přístup, můžete využít výhod sdíleného přístupového podpisu (SAS). SAS umožňuje sdílet určité objekty v úložišti pro předdefinovaný časový interval a s určitými oprávněními. Sdílený přístupový podpis umožňuje definovat:

- Interval, ve kterém je platný SAS, včetně počáteční čas a čas vypršení platnosti.
- Oprávnění udělená SAS. Například SAS na objekt blob může udělit uživateli oprávnění ke čtení a zápisu do tohoto objektu blob, ale ne odstranit oprávnění.
- Volitelná IP adresa nebo rozsah IP adres, ze kterých Azure Storage přijímá SAS. Můžete například zadat rozsah adres IP, které patří vaší organizaci. To poskytuje další míru zabezpečení pro vaše SAS.
- Protokol, přes který Azure Storage přijímá SAS. Tento volitelný parametr můžete použít k omezení přístupu ke klientům pomocí protokolu HTTPS.

SAS umožňuje sdílet obsah tak, jak chcete sdílet, aniž byste rozdávali klíče účtu úložiště. Vždy pomocí SAS ve vaší aplikaci je bezpečný způsob, jak sdílet prostředky úložiště bez ohrožení klíčů účtu úložiště.

Další informace o sdíleném přístupovém podpisu najdete [v tématu Používání sdílených přístupových podpisů](/azure/storage/common/storage-dotnet-shared-access-signature-part-1). 

## <a name="use-role-based-access-control"></a>Použití řízení přístupu na základě rolí
Dalším způsobem, jak spravovat přístup, je použití [řízení přístupu na základě rolí](/azure/role-based-access-control/overview) (RBAC). S RBAC se zaměřujete na to, abyste zaměstnancům poskytli přesná oprávnění, která potřebují, na základě potřeby znát a nejméně oprávnění zabezpečení. Příliš mnoho oprávnění může vystavit účet útočníkům. Příliš málo oprávnění znamená, že zaměstnanci nemohou pracovat efektivně. RBAC pomáhá řešit tento problém tím, že nabízí jemně odstupňovanou správu přístupu pro Azure. To je nezbytné pro organizace, které chtějí vynutit zásady zabezpečení pro přístup k datům.

Integrované role RBAC v Azure můžete použít k přiřazení oprávnění uživatelům. Můžete například použít přispěvatele účtu úložiště pro cloudové operátory, kteří potřebují spravovat účty úložiště a roli klasického přispěvatele účtu úložiště ke správě klasických účtů úložiště. Pro cloudové operátory, kteří potřebují spravovat virtuální počítače, ale ne účet virtuální sítě nebo úložiště, ke kterému jsou připojeni, je můžete přidat do role přispěvatele virtuálního počítače.

Organizace, které nevynucují řízení přístupu k datům pomocí funkcí, jako je Například RBAC, mohou udělovat více oprávnění, než je nezbytné pro jejich uživatele. To může vést k ohrožení zabezpečení dat tím, že někteří uživatelé přístup k datům, které by neměli mít na prvním místě.

Další informace o RBAC naleznete na adrese:

- [Správa přístupu pomocí RBAC a portálu Azure](/azure/role-based-access-control/role-assignments-portal)
- [Předdefinované role pro prostředky Azure](/azure/role-based-access-control/built-in-roles)
- [Průvodce zabezpečením azure úložiště](/azure/storage/common/storage-security-guide) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Použití šifrování na straně klienta pro data s vysokou hodnotou
Šifrování na straně klienta umožňuje programově šifrovat data při přenosu před odesláním do Služby Azure Storage a programově dešifrovat data při jejich načítání. To poskytuje šifrování dat při přenosu, ale také šifrování dat v klidovém stavu. Šifrování na straně klienta je nejbezpečnější způsob šifrování dat, ale vyžaduje, abyste v aplikaci provázli programové změny a zavedli procesy správy klíčů.

Šifrování na straně klienta také umožňuje mít výhradní kontrolu nad šifrovacími klíči. Můžete generovat a spravovat vlastní šifrovací klíče. Používá obálkovou techniku, kde knihovna klienta úložiště Azure generuje šifrovací klíč obsahu (CEK), který je pak zabalen (šifrovaný) pomocí šifrovacího klíče klíče (KEK). KEK je identifikován identifikátor klíče a může být asymetrický pár klíčů nebo symetrický klíč a lze spravovat místně nebo uloženy v [trezoru klíčů Azure](/azure/key-vault/key-vault-overview).

Šifrování na straně klienta je integrováno do knihoven klienta Java a .NET storage. Informace o šifrování v [klientských aplikacích a trezoru klíčů Azure pro Microsoft Azure Storage](/azure/storage/common/storage-client-side-encryption) najdete v tématu informace o šifrování dat v klientských aplikacích a generování a správě vlastních šifrovacích klíčů.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Povolení šifrování služby Úložiště pro data v klidovém stavu
Pokud je povoleno [šifrování služby úložiště](/azure/storage/common/storage-service-encryption) pro ukládání souborů, data jsou šifrována automaticky pomocí šifrování AES-256. Společnost Microsoft zpracovává veškeré šifrování, dešifrování a správu klíčů. Tato funkce je k dispozici pro typy redundance LRS a GRS.

## <a name="next-steps"></a>Další kroky

Tento článek vás seznámil s kolekcí doporučených postupů zabezpečení Azure Storage pro zabezpečení webových a mobilních aplikací PaaS. Další informace o zabezpečení nasazení PaaS najdete v tématu:

- [Zabezpečení nasazení PaaS](paas-deployments.md)
- [Zabezpečení webových a mobilních aplikací PaaS pomocí služby Azure App Services](paas-applications-using-app-services.md)
- [Zabezpečení databází PaaS v Azure](paas-applications-using-sql.md)

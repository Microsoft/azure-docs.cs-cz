---
title: Funkce zabezpečení, které lze použít s Azure Storage | Microsoft Docs
description: Tento článek obsahuje přehled základní funkce Azure zabezpečení, které lze použít s Azure Storage.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 9558f1ec0d8ccd83da764a0967fa83d93e1e6a02
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365358"
---
# <a name="azure-storage-security-overview"></a>Přehled zabezpečení služby Azure Storage
Azure Storage je řešení cloudového úložiště pro moderní aplikace, které se opírají o odolnost, dostupnost a škálovatelnost, aby mohly vyhovět potřebám zákazníků. Úložiště Azure poskytuje komplexní sadu funkcí zabezpečení. Můžete:

* Zabezpečení účtu úložiště pomocí řízení přístupu na základě Role (RBAC) a Azure Active Directory.
* Zabezpečení dat při přenosu mezi aplikací a Azure pomocí šifrování na straně klienta, HTTPS nebo SMB 3.0.
* Nastavte data se šifrují automaticky při zápisu do služby Azure Storage pomocí šifrování služby úložiště.
* Sada operačního systému a datové disky používat virtuální počítače (VM) k šifrování pomocí Azure Disk Encryption.
* Uděluje Delegovaný přístup k datové objekty ve službě Azure Storage pomocí sdílených přístupových podpisů (SASs).
* Analýza použijte ke sledování metoda ověřování, která používá uživatel při přístupu k úložišti.

Podrobnější podívejte se na zabezpečení ve službě Azure Storage, najdete v článku [Průvodce zabezpečením Azure Storage](../storage/common/storage-security-guide.md). Tato příručka obsahuje podrobné informace do funkce zabezpečení Azure Storage. Tyto funkce patří klíče účtu úložiště, šifrování dat při transitu i v rest a analytika úložiště.

Tento článek obsahuje přehled funkcí Azure zabezpečení, které můžete použít s Azure Storage. Odkazy na články poskytují podrobnosti o každé funkce tak další informace.

## <a name="role-based-access-control"></a>Řízení přístupu na základě rolí
Můžete pomoct zabezpečit váš účet úložiště pomocí řízení přístupu na základě Role. Omezení přístupu na základě [potřebovat znát](https://en.wikipedia.org/wiki/Need_to_know) a [nejnižší oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) Principy zabezpečení je nutné pro organizace, které chcete vynutit zásady zabezpečení pro přístup k datům. Tato oprávnění jsou udělena přiřazením příslušné role RBAC skupinám a aplikacím v určitých oboru. Můžete použít [předdefinované role RBAC](../role-based-access-control/built-in-roles.md), jako je Přispěvatel účet úložiště, přiřadit oprávnění pro uživatele.

Další informace:

* [Řízení přístupu na základě Role Azure Active Directory](../role-based-access-control/role-assignments-portal.md)

## <a name="delegated-access-to-storage-objects"></a>Delegovaný přístup k objektům úložiště
Sdílený přístupový podpis poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště. SAS znamená udělit, že klient v zadaném období a se zadanou sadou oprávnění omezená oprávnění k objektům v účtu úložiště. Tato omezená oprávnění můžete udělit bez nutnosti sdílet klíče pro přístup k účtu. 

SAS je identifikátor URI, který zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště v jeho parametry dotazu. Pro přístup k prostředkům úložiště s SAS, klient pouze musí zajistit SAS odpovídající konstruktor nebo metoda.

Další informace:

* [Vysvětlení modelu SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Vytváření a používání SAS s úložištěm Blob](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Šifrování během přenosu
Šifrování během přenosu je mechanismus ochrany dat při přenosu v sítích. S Azure Storage můžete zabezpečit data pomocí:

* [Šifrování transportní vrstvy](../storage/common/storage-security-guide.md#encryption-in-transit), jako je například HTTPS, při přenosu dat do nebo z Azure Storage.
* [Propojit šifrování](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), jako například šifrování protokolu SMB 3.0, pro Azure sdílené složky.
* [Šifrování na straně klienta](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), k šifrování dat, než se přenese do úložiště a k dešifrování dat po se přenáší z úložiště.

Další informace o šifrování na straně klienta:

* [Šifrování na straně klienta pro Microsoft Azure Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Ovládací prvky zabezpečení cloudu řady: šifrování dat při přenosu](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Šifrování v klidovém stavu
Pro mnoho společností [šifrování dat v klidovém stavu](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) je povinný krok směrem k suverenity data o ochraně osobních údajů a dodržování předpisů a data. Tři Azure funkce poskytují šifrování dat, který je v klidovém stavu:

* [Šifrování služby úložiště](../storage/common/storage-security-guide.md#encryption-at-rest) umožňuje požadovat, aby službu úložiště automaticky šifrování dat při zápisu do služby Azure Storage.
* [Šifrování na straně klienta](../storage/common/storage-security-guide.md#client-side-encryption) také poskytuje funkci šifrování v klidovém stavu.
* [Azure Disk Encryption](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) umožňuje šifrování disků operačního systému a datové disky, které se virtuální počítač IaaS používá.

Další informace o šifrování služby úložiště:

* [Azure šifrování služby úložiště](https://azure.microsoft.com/services/storage/) je k dispozici pro [úložiště objektů Azure Blob](https://azure.microsoft.com/services/storage/blobs/). Podrobnosti na jiné typy úložiště Azure najdete v tématu [Azure Files](https://azure.microsoft.com/services/storage/files/), [disku (Premium Storage)](https://azure.microsoft.com/services/storage/premium-storage/), [tabulky úložiště](https://azure.microsoft.com/services/storage/tables/), a [fronty úložiště](https://azure.microsoft.com/services/storage/queues/).
* [Šifrování služby úložiště Azure pro Data v klidovém stavu](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption pro virtuální počítače vám pomůže adresu zabezpečení organizace a požadavky na dodržování předpisů. Vaše disky virtuálního počítače (včetně spouštěcí a datovými disky), zašifruje pomocí klíče a zásady, které řídí v [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Šifrování disku pro virtuální počítače funguje pro operační systémy Linux a Windows. Používá také Key Vault můžete zabezpečit, správu a audit použití klíče pro šifrování disku. Všechna data v vaše disky virtuálních počítačů je zašifrovaná přinejmenším pomocí šifrování standardní technologie v účtech úložiště Azure. Šifrování disku řešení pro systém Windows je založeno na [Microsoft BitLocker Drive Encryption](https://technet.microsoft.com/library/cc732774.aspx), a Linux řešení je založena na [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Další informace:

* [Azure Disk Encryption pro systém Windows a virtuální počítače Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Azure Key Vault
Používá Azure Disk Encryption [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) vám pomohou řídit a spravovat disku šifrovací klíče a tajné klíče v rámci vašeho předplatného trezoru klíčů. Kromě toho zajišťuje jsou šifrování všech dat v disky virtuálních počítačů umístěných v úložišti Azure. Měli byste použít Key Vault auditovat klíče a použití zásad.

Další informace:

* [Co je Azure Key Vault?](../key-vault/key-vault-whatis.md)
* [Začínáme s Azure Key Vault](../key-vault/key-vault-get-started.md)

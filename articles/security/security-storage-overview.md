---
title: Funkce zabezpečení, které lze použít s Azure Storage | Dokumentace Microsoftu
description: Tento článek obsahuje základní informace o základní funkce zabezpečení Azure, které lze použít s Azure Storage.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2019
ms.author: terrylan
ms.openlocfilehash: ec0e8ae1bf657cda59f3d133db23106436e184e3
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56326752"
---
# <a name="azure-storage-security-overview"></a>Přehled zabezpečení služby Azure Storage

Azure Storage je řešení cloudového úložiště pro moderní aplikace, které se opírají o odolnost, dostupnost a škálovatelnost, aby mohly vyhovět potřebám zákazníků. Azure Storage nabízí komplexní sadu funkcí zabezpečení. Můžete:

* Zabezpečení účtu úložiště pomocí řízení přístupu na základě Role (RBAC) a Azure Active Directory.
* Zabezpečení dat během přenosu mezi aplikací a službou Azure pomocí šifrování na straně klienta, HTTPS nebo SMB 3.0.
* Nastavení data se šifrují automaticky při zápisu do služby Azure Storage pomocí Storage Service Encryption.
* Nastavení operačního systému a datové disky používané virtuálními počítači (VM) k šifrování pomocí Azure Disk Encryption.
* Udělte delegovaného přístupu k datovým objektům ve službě Azure Storage s použitím sdílených přístupových podpisů (SASs).
* Pomocí analýzy můžete sledovat způsob ověřování, který používá uživatel při přístupu k úložišti.

Podrobnější přehled zabezpečení ve službě Azure Storage, najdete v článku [Průvodci zabezpečením Azure Storage](../storage/common/storage-security-guide.md). Tato příručka obsahuje podrobné informace o funkcích zabezpečení služby Azure Storage. Tyto funkce patří klíče účtu úložiště, šifrování dat během přenosu a neaktivních uložených dat a analýza úložiště.


Tento článek obsahuje přehled funkcí zabezpečení služby Azure, které můžete použít se službou Azure Storage. Odkazy na články poskytují podrobnosti o každé funkce tak další informace.

## <a name="role-based-access-control"></a>Řízení přístupu na základě rolí

Můžete pomoct zabezpečit svůj účet úložiště pomocí řízení přístupu na základě rolí. Omezení přístupu na základě [znát](https://en.wikipedia.org/wiki/Need_to_know) a [nejnižší možná oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) principů zabezpečení je nutné pro organizace, které chcete vynutit zásady zabezpečení pro přístup k datům. Tato přístupová práva jsou uděluje přiřazením příslušné role RBAC pro skupiny nebo aplikace v určitém rozsahu. Můžete použít [předdefinované role RBAC](../role-based-access-control/built-in-roles.md), jako je Přispěvatel účtů úložiště, přiřadit oprávnění pro uživatele.

Další informace:

* [Řízení přístupu na základě rolí Azure Active Directory](../role-based-access-control/role-assignments-portal.md)

## <a name="delegated-access-to-storage-objects"></a>Delegovaný přístup k úložišti objektů

Sdílený přístupový podpis poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště. SAS znamená, že můžete udělit že klienta omezená oprávnění k objektům v účtu úložiště během zadaného období a s konkrétní sadou oprávnění. Omezená oprávnění může udělit aniž byste museli sdílet přístupové klíče vašeho účtu.

PODPIS je identifikátor URI, který zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště v jeho parametry dotazu. Pro přístup k prostředkům úložiště pomocí sdíleného přístupového podpisu, klient musí jen zadejte SAS odpovídajícího konstruktoru nebo metody.

Další informace:

* [Vysvětlení modelu SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Vytvoření a použití SAS s úložištěm objektů Blob](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Šifrování během přenosu

Šifrování během přenosu sítí je mechanismus ochrany dat při přenosu napříč sítěmi. S Azure Storage můžete zabezpečit data pomocí:

* [Šifrování na úrovni přenosu](../storage/common/storage-security-guide.md#encryption-in-transit), jako je například HTTPS, při přenosu dat do nebo z úložiště Azure.
* [Propojí šifrování](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), jako například šifrování protokolu SMB 3.0, pro Azure sdílené složky.
* [Šifrování na straně klienta](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage)pro šifrování dat před přenosu do služby Storage a k dešifrování dat po převedení mimo úložiště.

Další informace o šifrování na straně klienta:

* [Šifrování na straně klienta pro službu Microsoft Azure Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Řada – ovládací prvky zabezpečení cloudu: Šifrování přenášených dat](https://cloudblogs.microsoft.com/microsoftsecure/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Pro mnoho společností [šifrování dat v klidovém stavu](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) je povinný krok suverenita dat o ochraně osobních údajů, dodržování předpisů a data. Tři funkce Azure poskytují šifrování dat, která jsou v klidovém stavu:

* [Šifrování služby Storage](../storage/common/storage-security-guide.md#encryption-at-rest) bude vždy povolené a při zápisu do služby Azure Storage automaticky šifruje data služby úložiště.
* [Šifrování na straně klienta](../storage/common/storage-security-guide.md#client-side-encryption) také poskytuje funkci šifrování v klidovém stavu.
* [Azure Disk Encryption](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) umožňuje šifrovat disky s operačním systémem a datové disky, které používá virtuální počítač IaaS.

Další informace o šifrování služby Storage:

* [Azure Storage Service Encryption](https://azure.microsoft.com/services/storage/) je k dispozici pro [úložiště objektů Blob v Azure](https://azure.microsoft.com/services/storage/blobs/). Podrobnosti na jiné typy úložiště Azure najdete v tématu [Azure Files](https://azure.microsoft.com/services/storage/files/), [Table storage](https://azure.microsoft.com/services/storage/tables/), a [Queue storage](https://azure.microsoft.com/services/storage/queues/).
* [Šifrování služby Azure Storage pro neaktivní uložená Data](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure Disk Encryption pro virtuální počítače pomáhá splnit organizační zabezpečení a požadavky na dodržování předpisů. Šifrování disků virtuálních počítačů (včetně spouštěcích a datových disků) pomocí klíčů a zásad, které určujete ve [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Disk Encryption pro virtuální počítače funguje pro operační systémy Linux a Windows. Také využívá služby Key Vault můžete chránit, spravovat a kontrolovat použití klíče pro šifrování disků. Všechna data v disky virtuálních počítačů v klidovém stavu zašifrovaná pomocí standardní šifrovací technologie v rámci účtů úložiště Azure. Řešení Disk Encryption pro Windows je založené na [Microsoft BitLocker Drive Encryption](https://technet.microsoft.com/library/cc732774.aspx), a řešení pro Linux je založen na [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Další informace

* [Azure Disk Encryption pro Windows a Linuxem v režimu IaaS Virtual Machines](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="firewalls-and-virtual-networks"></a>Brány firewall a virtuální sítě

Azure storage umožňuje povolit pravidla brány firewall pro vaše účty úložiště. Jednou povoleno, zablokují se příchozí požadavky na data, včetně požadavků od ostatních služeb Azure. Můžete nakonfigurovat výjimky pro povolení provozu. Pravidla brány firewall může být povoleno na existující účty úložiště nebo při vytváření.

Tato funkce by měla použít k zabezpečení vašich účtů úložiště pro konkrétní sadu povolených sítí.

Další informace o Azure storage virtuální sítí a bran firewall, přečtěte si článek [konfigurace brány firewall úložiště Azure a virtuální sítě](../storage/common/storage-network-security.md)

## <a name="azure-data-box"></a>Azure Data Box

Zařízení Data Box, Data Box Disk a Data Box Heavy vám pomohou přenést velké objemy dat do Azure v případě, že nemůžete použít síť. Tato zařízení přenosu dat offline se dodávají mezi vaší organizací a datového centra Azure. Přenášená data pomáhají chránit s využitím šifrování AES a po nahrání provádějí proces důkladné sanitizace, který odstraní vaše data ze zařízení.

Data Box Edge a Data Box Gateway jsou produkty pro online přenos dat, které fungují jako brány síťového úložiště a umožňují správu dat mezi vaší lokalitou a Azure. Data Box Edge je místní síťové zařízení, které přenáší data do a z Azure a při zpracování dat využívá hraniční výpočetní prostředky s podporou umělé inteligence (AI). Data Box Gateway je virtuální zařízení s funkcemi brány úložiště.

Další informace:

* [Azure Data Box](https://azure.microsoft.com/services/storage/databox/)
* [Azure Data Box Edge](../databox-online/data-box-edge-overview.md)
* [Azure Data Box Gateway](..//databox-online/data-box-gateway-overview.md)

## <a name="advanced-threat-protection"></a>Rozšířená ochrana před internetovými útoky

Azure Storage poskytuje rozšířené ochrany před internetovými útoky pro další úroveň inteligentního zabezpečení, která zjistí a potenciálně nebezpečné pokusy o přístup nebo zneužití účtu úložiště. Rozšířené ochrany před internetovými útoky monitorování diagnostické protokoly služby Azure Storage pro podezřelé číst, zapsat nebo odstranit požadavky na úložiště objektů Blob.

Rozšířené ochrany před internetovými útoky výstrahy můžete zobrazit v [Azure Security Center](https://azure.microsoft.com/services/security-center/). Azure Security Center poskytuje podrobnosti o podezřelé aktivity zjištěna a doporučuje akcích pro šetření a náprava potenciálních ohrožení.

Další informace:

* [Advanced Threat Protection Přehled služby Azure Storage](../storage/common/storage-advanced-threat-protection.md)

## <a name="azure-key-vault"></a>Azure Key Vault

Azure Disk Encryption používá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) umožňují řídit a spravovat klíče pro šifrování disků a tajné kódy ve vašem předplatném služby key vault. Také zajistí, že všechna data na discích virtuálních počítačů jsou zašifrovaná rest ve službě Azure Storage. Auditovat klíče a použití zásad, měli byste použít Key Vault.

Další informace

* [Co je Azure Key Vault?](../key-vault/key-vault-overview.md)

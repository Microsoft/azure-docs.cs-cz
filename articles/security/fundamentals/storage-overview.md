---
title: Funkce zabezpečení, které je možné používat s Azure Storage | Microsoft Docs
description: Tento článek obsahuje přehled základních funkcí zabezpečení Azure, které je možné používat s Azure Storage.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: e273d913d07d8d7b77289695a4a28a478a64123a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350299"
---
# <a name="azure-storage-security-overview"></a>Přehled zabezpečení Azure Storage

Tento článek obsahuje přehled funkcí zabezpečení Azure, které můžete používat s Azure Storage. Azure Storage je řešení cloudového úložiště pro moderní aplikace, které se opírají o odolnost, dostupnost a škálovatelnost, aby mohly vyhovět potřebám zákazníků. Azure Storage poskytuje komplexní sadu funkcí zabezpečení. Můžete:

* Zabezpečte účet úložiště pomocí Access Control na základě rolí (RBAC) a Azure Active Directory.
* Zabezpečit data při přenosu mezi aplikací a Azure pomocí šifrování na straně klienta, HTTPS nebo SMB 3.0.
* Nastavení dat, která se mají automaticky šifrovat při zápisu do Azure Storage pomocí Šifrování služby Storage
* Nastavte operační systém a datové disky používané virtuálními počítači k šifrování pomocí Azure Disk Encryption.
* Udělte delegovaný přístup k datovým objektům v Azure Storage pomocí sdílených přístupových podpisů (SASs).
* Pomocí analýzy můžete sledovat metodu ověřování, kterou někdo používá při přístupu k úložišti.

Podrobnější přehled o zabezpečení v Azure Storage najdete v [Průvodci zabezpečením Azure Storage](../../storage/common/storage-security-guide.md). Tato příručka obsahuje podrobné podrobně k funkcím zabezpečení Azure Storage. Mezi tyto funkce patří klíče účtu úložiště, šifrování dat při přenosu a v klidovém provozu a analýza úložiště.

## <a name="role-based-access-control"></a>Řízení přístupu na základě rolí

Účet úložiště můžete zabezpečit pomocí Access Control na základě rolí. Omezení přístupu na základě [potřebných znalostí](https://en.wikipedia.org/wiki/Need_to_know) a [minimálních](https://en.wikipedia.org/wiki/Principle_of_least_privilege) principů zabezpečení oprávnění je nezbytné pro organizace, které chtějí vynutilit zásady zabezpečení pro přístup k datům. Tato přístupová práva jsou udělována přiřazením příslušné role RBAC do skupin a aplikací v určitém rozsahu. K přiřazení oprávnění uživatelům můžete použít [předdefinované role RBAC](/azure/role-based-access-control/built-in-roles), jako je Přispěvatel účtu úložiště.

Další informace:

* [Azure Active Directory Access Control na základě rolí](/azure/role-based-access-control/role-assignments-portal)

## <a name="delegated-access-to-storage-objects"></a>Delegovaný přístup k objektům úložiště

Sdílený přístupový podpis poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště. SAS znamená, že můžete klientovi udělit omezená oprávnění k objektům ve vašem účtu úložiště po určitou dobu a se zadanou sadou oprávnění. Tato omezená oprávnění můžete udělit bez nutnosti sdílení přístupových klíčů k účtu.

SAS je identifikátor URI, který v parametrech dotazu zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště. Chcete-li získat přístup k prostředkům úložiště pomocí SAS, klient musí zadat SAS pouze příslušnému konstruktoru nebo metodě.

Další informace:

* [Principy modelu SAS](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
* [Vytvoření a použití SAS s úložištěm objektů BLOB](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

## <a name="encryption-in-transit"></a>Šifrování při přenosu

Šifrování při přenosu je mechanismus ochrany dat při přenosu mezi sítěmi. Pomocí Azure Storage můžete zabezpečit data pomocí:

* [Šifrování na úrovni přenosu](/azure/storage/common/storage-security-guide#encryption-in-transit), jako je například https, při přenosu dat do nebo z Azure Storage.
* [Šifrování kabelů](/azure/storage/common/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), jako je například šifrování SMB 3,0, pro sdílené složky Azure.
* [Šifrování na straně klienta](/azure/storage/common/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), aby se data zašifroval před přenosem do úložiště a dešifrují data poté, co se přenesou z úložiště.

Další informace o šifrování na straně klienta:

* [Šifrování na straně klienta pro Microsoft Azure Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* řady ovládacích prvků zabezpečení @no__t 0Cloud: Šifrování dat při přenosu @ no__t-0

## <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

V případě mnoha organizací je [šifrování dat v klidovém](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) případě povinný krok k ochraně dat, dodržování předpisů a suverenity dat. Tři funkce Azure poskytují šifrování dat, která jsou v klidovém znění:

* [Šifrování služby Storage](/azure/storage/common/storage-security-guide#encryption-at-rest) je vždycky povolená a při zápisu do Azure Storage automaticky šifruje data služby Storage.
* [Šifrování na straně klienta](/azure/storage/common/storage-security-guide#client-side-encryption) také poskytuje funkci šifrování v klidovém umístění.
* [Azure Disk Encryption](/azure/storage/common/storage-security-guide#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) slouží k šifrování disků s operačním systémem a datových disků, které používá virtuální počítač s IaaS.

Další informace o Šifrování služby Storage:

* K dispozici je [šifrování služby Azure Storage](https://azure.microsoft.com/services/storage/) pro [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/). Podrobnosti o dalších typech úložiště Azure najdete v tématech [soubory Azure](https://azure.microsoft.com/services/storage/files/), [úložiště tabulek](https://azure.microsoft.com/services/storage/tables/)a [Queue Storage](https://azure.microsoft.com/services/storage/queues/).
* [Šifrování služby Azure Storage pro neaktivní neaktivní data](/azure/storage/common/storage-service-encryption)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure Disk Encryption pro virtuální počítače pomáhá řešit požadavky organizace na zabezpečení a dodržování předpisů. Šifruje disky virtuálních počítačů (včetně spouštěcích a datových disků) pomocí klíčů a zásad, které ovládáte v [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Šifrování disku pro virtuální počítače funguje pro operační systémy Linux a Windows. Také používá Key Vault, které vám pomůžou chránit, spravovat a auditovat používání klíčů pro šifrování disků. Všechna data na discích virtuálních počítačů jsou v klidovém stavu zašifrovaná pomocí standardní šifrovací technologie ve vašich účtech Azure Storage. Řešení pro šifrování disků pro Windows je založené na [Microsoft nástroj BitLocker Drive Encryption](https://technet.microsoft.com/library/cc732774.aspx)a řešení pro Linux je založené na [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Víc se uč

* [Přehled Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview)

## <a name="firewalls-and-virtual-networks"></a>Brány firewall a virtuální sítě

Azure Storage umožňuje povolit pravidla brány firewall pro účty úložiště. Po povolení budou zablokovat příchozí požadavky na data, včetně požadavků z jiných služeb Azure. Můžete nakonfigurovat výjimky pro povolení provozu. Pravidla brány firewall můžou být povolená u stávajících účtů úložiště nebo v době vytváření.

Tuto funkci byste měli používat k zabezpečení účtů úložiště na určitou sadu povolených sítí.

Další informace o branách firewall a virtuálních sítích Azure Storage najdete v článku [konfigurace Azure Storage bran firewall a virtuálních sítí](/azure/storage/common/storage-network-security) .

## <a name="azure-data-box"></a>Azure Data Box

Zařízení Data Box, Data Box Disk a Data Box Heavy vám pomohou přenést velké objemy dat do Azure v případě, že nemůžete použít síť. Tato zařízení offline pro přenos dat se dodávají mezi vaší organizací a datovým centrem Azure. Přenášená data pomáhají chránit s využitím šifrování AES a po nahrání provádějí proces důkladné sanitizace, který odstraní vaše data ze zařízení.

Data Box Edge a Data Box Gateway jsou produkty pro online přenos dat, které fungují jako brány síťového úložiště a umožňují správu dat mezi vaší lokalitou a Azure. Data Box Edge je místní síťové zařízení, které přenáší data do a z Azure a při zpracování dat využívá hraniční výpočetní prostředky s podporou umělé inteligence (AI). Data Box Gateway je virtuální zařízení s funkcemi brány úložiště.

Další informace:

* [Azure Data Box](https://azure.microsoft.com/services/storage/databox/)
* [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview)
* [Azure Data Box Gateway](/azure/databox-online/data-box-gateway-overview)

## <a name="advanced-threat-protection"></a>Rozšířená ochrana před internetovými útoky

Azure Storage poskytuje rozšířenou ochranu před internetovými útoky pro další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k vašemu účtu úložiště nebo jeho zneužití. Rozšířená ochrana před internetovými útoky Azure Storage diagnostické protokoly pro podezřelé požadavky na čtení, zápis a odstranění do úložiště objektů BLOB.

Výstrahy rozšířené ochrany před internetovými útoky si můžete prohlédnout z [Azure Security Center](https://azure.microsoft.com/services/security-center/). Azure Security Center poskytuje podrobnosti o zjištěné podezřelé aktivitě a doporučuje akce k prozkoumání a nápravě potenciální hrozby.

Další informace:

* [Přehled Azure Storage rozšířené ochrany před internetovými útoky](/azure/storage/common/storage-advanced-threat-protection)

## <a name="azure-key-vault"></a>Azure Key Vault

Azure Disk Encryption používá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) k řízení a správě šifrovacích klíčů a tajných klíčů disků v rámci předplatného trezoru klíčů. Také zajišťuje, že všechna data v discích virtuálních počítačů jsou v klidovém stavu zašifrovaná v Azure Storage. K auditování klíčů a používání zásad byste měli použít Key Vault.

Víc se uč

* [Co je Azure Key Vault?](/azure/key-vault/key-vault-overview)

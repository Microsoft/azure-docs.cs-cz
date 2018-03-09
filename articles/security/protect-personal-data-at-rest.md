---
title: "Azure ochrana osobních dat v klidovém stavu s šifrováním | Microsoft Docs"
description: "Tento článek slouží v effforts pro dosažení souladu s požadavky na odvětvím nebo státní například obecné Data Protection nařízení (GDPR)."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 37da3d91df4be871d6647b6290768ea4999a5010
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="azure-encryption-technologies-protect-personal-data-at-rest-with-encryption"></a>Azure šifrovací technologie: ochrana osobních údajů v klidovém stavu se šifrováním

Tento článek vám pomůže pochopit a použít technologie šifrování Azure k zabezpečení dat v klidovém stavu.

Šifrování dat v klidovém stavu je nezbytné jako osvědčený postup chránit důvěrné nebo osobní data a, aby splňoval požadavky na dodržování předpisů a data o ochraně osobních údajů. Šifrování v klidovém stavu slouží k útočník zabránit v přístupu k nešifrovaná data zajištěním data se šifrují, pokud na disku. Informace obsažené v tomto článku slouží k zajištění souladu s obecné Data Protection nařízení (GDPR).

## <a name="scenario"></a>Scénář 

Velké výletních společnosti, centrálou ve Spojených státech amerických, je rozšířit jeho operací a nabídnout itineráře v Středomoří a baltský moři, jakož i Britské ostrovy. Pro podporu těchto úsilí, získala menší výletních Víceřádkový na základě v Itálii, Německo, Dánsko a Spojeném království

Společnost používá Microsoft Azure k ukládání firemních dat v cloudu. Například to může zahrnovat informace zákazníků nebo zaměstnanců:

- adresy
- telefonní čísla
- daň identifikační čísla
- informace o kreditní kartě

Společnosti musí ochrany osobních údajů zákazníků a dat při zpřístupnění dat pro tyto oddělení, které je třeba ji. (například oddělení mzdy a rezervace)

Na řádku výletních také udržuje velké databáze potřebu a věrnost program členů, která zahrnuje osobní údaje ke sledování vztahů se zákazníky aktuální a starší.

### <a name="problem-statement"></a>Popis problému

Společnosti musí ochrany osobních údajů zaměstnanců a zákazníků osobních údajů při zpřístupnění dat pro tyto oddělení, které je třeba (například oddělení mzdy a rezervace). Tato osobní data je uložený mimo firemní řízenou datového centra a není pod kontrolou fyzické společnosti.

### <a name="company-goal"></a>Cílem společnosti

Jako součást strategie zabezpečení víceúrovňová obrany zabezpečení je cílem společnosti zajistit, že všechny zdroje dat, které obsahují osobní data jsou zašifrovaná, včetně těch, které se nacházejí v cloudovém úložišti. Pokud neoprávněné osoby získat přístup k osobním datům, musí být ve formuláři, který bude zobrazovat nečitelná. Použití šifrování by měla být snadná nebo transparentní – pro uživatele a správce.

## <a name="solutions"></a>Řešení

Azure services poskytují několik nástrojů a technologie, které vám umožní chránit osobní data v klidovém stavu šifrování ho.

### <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) poskytuje zabezpečené úložiště pro klíče, které slouží k šifrování dat v klidovém stavu uložených v služby Azure a je doporučenou klíče úložiště a správu řešení. Správa klíčů pro šifrování je nezbytné pro zabezpečení uložená data.

#### <a name="how-do-i-use-azure-key-vault-to-protect-keys-that-encrypt-personal-data"></a>Jak používat Azure Key Vault k ochraně klíče, které šifrování osobní údaje?

Pokud chcete používat Azure Key Vault, potřebujete předplatné účet Azure. Budete také potřebovat nainstalovaný Azure PowerShell. Zahrnuje následující kroky pomocí rutin prostředí PowerShell provést následující akce:

1. Připojte se ke svým předplatným

2. Vytvořte trezor klíčů

3. Přidejte k trezoru klíč nebo tajný klíč

4. Registrace aplikace, které se používají trezor klíčů s Azure Active Directory

5. Autorizace aplikací pro použití klíče nebo tajného klíče

K vytvoření trezoru klíčů, použijte rutinu prostředí PowerShell New-AzureRmKeyVault. Přiřadí název trezoru, název skupiny prostředků a zeměpisné umístění. Název trezoru budete používat při správě klíčů prostřednictvím jiné rutiny. Aplikace, které používají trezor prostřednictvím REST API použije identifikátor URI trezoru.

Azure Key Vault můžete zadat softwarově chráněný klíč, nebo můžete importovat existující klíč v. Soubor PFX. Můžete také ukládat tajné klíče (hesla) v trezoru.

Můžete také vygenerovat klíč v místním HSM a jeho přenesení do HSM ve službě Key Vault, aniž by klíč opustil hranice HSM.

Pro podrobné pokyny týkající se použití Azure Key Vault, postupujte podle kroků v [Začínáme s Azure Key Vault.](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)

Seznam rutin prostředí PowerShell použít s Azure Key Vault najdete v tématu [AzureRM.KeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

### <a name="azure-disk-encryption-for-windows"></a>Azure Disk Encryption pro Windows

[Azure Disk Encryption pro Windows a virtuálních počítačů IaaS Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) chrání osobní data v klidovém stavu na virtuálních počítačích Azure a integruje se službou Azure Key Vault. Používá Azure Disk Encryption [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) v systému Windows a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) v systému Linux k šifrování operačního systému a datové disky. Azure Disk Encryption je podporována v systému Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 a u klientů Windows 8 a Windows 10.

#### <a name="how-do-i-use-azure-disk-encryption-to-protect-personal-data"></a>Jak používat Azure Disk Encryption na ochranu osobních údajů?

Pokud chcete používat Azure Disk Encryption, potřebujete předplatné účet Azure. Chcete-li Azure Disk Encryption pro systém Windows a virtuální počítače s Linuxem, postupujte takto:

1. Pomocí šablony Azure Disk Encryption Resource Manageru, prostředí PowerShell nebo rozhraní příkazového řádku (CLI) povolte šifrování disku a zadejte nastavení šifrování. 

2. Udělit přístup na platformu Azure ke čtení materiálu šifrování z trezoru klíčů.

3. Zadejte identity aplikací Azure Active Directory (AAD) k zápisu materiálu šifrovací klíče do trezoru klíčů.

Azure bude aktualizace virtuálního počítače a konfigurace trezoru klíčů a nastavit šifrované virtuálního počítače.

Při nastavování trezoru klíčů pro podporu Azure Disk Encryption, můžete přidat klíče šifrovací klíč (KEK) pro zvýšení zabezpečení a pro podporu zálohování šifrované virtuálních počítačů.

![](media/protect-personal-data-at-rest/create-key.png)

Podrobné pokyny pro konkrétní nasazení scénáře a uživatelského prostředí jsou uvedené v [Azure Disk Encryption pro systém Windows a virtuálních počítačů IaaS Linux.](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="azure-storage-service-encryption"></a>Šifrování služby Azure Storage

[Azure Storage Service šifrování (SSE) pro Data v klidovém stavu](https://docs.microsoft.com/azure/storage/storage-service-encryption) pomáhá chránit a ochranu dat, aby splňovaly vaše organizace závazky zabezpečení a dodržování předpisů. Úložiště Azure automaticky šifruje vaše data pomocí šifrování AES 256 bitů před uložením do úložiště a dešifruje ji před načtení. Tato služba je k dispozici pro soubory a objektů BLOB služby Azure.

#### <a name="how-do-i-use-storage-service-encryption-to-protect-personal-data"></a>Jak použít šifrování služby úložiště na ochranu osobních údajů?

Pokud chcete povolit šifrování služby úložiště, postupujte takto:

1. Přihlaste se k portálu Azure.

2. Vyberte účet úložiště.

3. V nastavení vyberte v části služby objektů Blob šifrování.

4. V části Souborová služba vyberte šifrování.

Po kliknutí na tlačítko Nastavení šifrování, můžete povolit nebo zakázat šifrování služby úložiště.

![](media/protect-personal-data-at-rest/storage-service-encryption.png)

Nová data se budou šifrovat. Data v existující soubory v rámci tohoto účtu úložiště zůstat nezašifrovaný.

Po povolení šifrování, zkopírujte data do účtu úložiště pomocí jedné z následujících metod:

1. Kopírování objektů BLOB nebo soubory s [nástroj příkazového řádku AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy).

2. [Připojit sdílenou složku pomocí protokolu SMB](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-windows) proto můžete použít nástroj, například Robocopy pro kopírování souborů.

3. Zkopírujte soubor nebo objekt blob dat do a z úložiště objektů blob nebo mezi účtů úložiště pomocí [knihovny klienta úložiště, jako je například .NET](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-blobs).

4.  Použití [Storage Explorer](https://docs.microsoft.com/azure/storage/storage-explorers) nahrát objektů BLOB do účtu úložiště s povolit šifrování.

### <a name="transparent-data-encryption"></a>Transparentní šifrování dat

Transparentní šifrování šifrování dat (TDE) je funkce v produktech SQL Azure, pomocí kterého můžete šifrovat data na úrovni databáze a serveru. Ve výchozím nastavení na všechny nově vytvořené databáze je teď povolené šifrování TDE. Šifrování TDE provádí v reálném čase vstupní/výstupní šifrování a dešifrování souborů protokolu a data.

#### <a name="how-do-i-use-tde-to-protect-personal-data"></a>Jak použít šifrování TDE na ochranu osobních údajů?

Šifrování TDE prostřednictvím portálu Azure můžete nakonfigurovat pomocí rozhraní REST API nebo pomocí prostředí PowerShell. Pokud chcete povolit šifrování TDE na existující databázi pomocí portálu Azure, postupujte takto:

1. Navštívit portál Azure na adrese <https://portal.azure.com> a přihlaste se pomocí účtu správce Azure nebo přispěvatelem.

2. V levém informační zpráva klikněte na Procházet a potom klikněte databází SQL.

3. V levém podokně vybraných databází SQL klikněte na své databázi uživatelů.

4. V okně databáze klikněte na tlačítko všechna nastavení.

5. V okně nastavení klikněte na část šifrování transparentní dat a otevřete okno transparentní dat šifrování.

6. V okně šifrování dat přesunout tlačítko pro šifrování dat na a pak klikněte na Uložit (v horní části stránky) a použijte nastavení. Průběh transparentní šifrování dat bude Přibližná stav šifrování.

![Povolení šifrování dat](media/protect-personal-data-at-rest/turn-data-encryption-on.png)

Informace o dešifrování TDE chráněné databáze a informace a pokyny o tom, jak povolit šifrování TDE najdete v článku [transparentní šifrování dat s Azure SQL Database.](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database)

## <a name="summary"></a>Souhrn

Společnost můžete provést jeho cílem šifrování osobních dat uložených v cloudu Azure. Dělají to pomocí Azure Disk Encryption k ochraně celé svazky. To může zahrnovat jak soubory operačního systému a datové soubory, které obsahují osobní identifikovatelné údaje a další citlivá data. Azure šifrování služby úložiště můžete použít k ochraně osobní data, která je uložená v souborech a objekty BLOB. Transparentní šifrování dat pro data, která je uložená v databázích Azure SQL, poskytuje ochranu před neoprávněným ohrožení osobních údajů.

K ochraně klíčů, které se používají k šifrování dat v Azure, může společnost použít Azure Key Vault. To zjednodušuje proces správy klíčů a umožňuje společnosti umožní zachovat kontrolu nad klíče, které k přístupu a šifrování osobní data.

## <a name="next-steps"></a>Další postup

- [Průvodce odstraňováním potíží Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-tsg)

- [Šifrování virtuálního počítače Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption?toc=%2fazure%2fsecurity%2ftoc.json)

- [Šifrování dat v Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-encryption)

- [Azure Cosmos DB databáze šifrování v klidovém stavu](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)

---
title: Porovnání souborů a Azure NetApp Files Azure | Microsoft Docs
description: Porovnání souborů a Azure NetApp Files Azure
author: jeffpatt24
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/19/2021
ms.author: jeffpatt
ms.openlocfilehash: 8ebd667b9f3f4ec1a6db4481e42282b3d8bae97d
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104776173"
---
# <a name="azure-files-and-azure-netapp-files-comparison"></a>Porovnání souborů a Azure NetApp Files Azure

Tento článek popisuje porovnání souborů a Azure NetApp Files Azure. 

Většina úloh, které vyžadují cloudové úložiště souborů, se dobře hodí jak na soubory nebo Azure NetApp Files Azure. Informace uvedené v tomto článku vám pomůžou zjistit, co nejlépe vyhovuje vašim úlohám. Další informace najdete v dokumentaci k [souborům](https://docs.microsoft.com/azure/storage/files/) a [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/) Azure.

## <a name="features"></a>Funkce

| Kategorie | Azure Files | Azure NetApp Files |
|---------|-------------------------|---------|
| Popis | Služba [soubory Azure](https://azure.microsoft.com/services/storage/files/) je plně spravovaná, vysoce dostupná služba a je optimalizovaná pro úlohy s náhodným přístupem s aktualizacemi místních dat.<br><br> Služba soubory Azure je postavená na stejné platformě Azure Storage jako jiné služby, jako jsou objekty blob Azure. | [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) je plně spravovaná, vysoce dostupná služba na podnikové úrovni, která dokáže zvládnout nejnáročnější a vysoce výkonné úlohy s nízkou latencí, které vyžadují pokročilé možnosti správy dat. Umožňuje migraci úloh, které se považují za "un-migrovat" bez.<br><br>  ANF je postavená na holých počítačích NetApp s operačním systémem ONTAP, který běží v datovém centru Azure pro konzistentní prostředí Azure a místní jako výkon. |
| Protokoly | Premium<br><ul><li>SMB 2,1, 3,0</li><li>NFS 4,1 (Preview)</li></ul><br>Standard<br><ul><li>SMB 2,1, 3,0</li><li>REST</li></ul><br> Další informace najdete v tématu [dostupné protokoly sdílení souborů](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols). | Všechny úrovně<br><ul><li>SMB 1, 2. x, 3. x</li><li>NFS 3,0, 4,1</li><li>Přístup přes duální protokol (NFSv3/SMB)</li></ul><br> Další informace najdete v tématu Postup vytvoření svazků [NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes), [SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb)nebo [duálního protokolu](https://docs.microsoft.com/azure/azure-netapp-files/create-volumes-dual-protocol) . |
| Dostupnost oblasti | Premium<br><ul><li>30 a oblastí</li></ul><br>Standard<br><ul><li>Všechny oblasti</li></ul><br> Další informace najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=storage). | Všechny úrovně<br><ul><li>25 a oblasti</li></ul><br> Další informace najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=storage). |
| Redundance | Premium<br><ul><li>LRS</li><li>ZRS</li></ul><br>Standard<br><ul><li>LRS</li><li>ZRS</li><li>GRS</li><li>GZRS</li></ul><br> Další informace najdete v tématu [redundance](https://docs.microsoft.com/azure/storage/files/storage-files-planning#redundancy). | Všechny úrovně<br><ul><li>Integrované místní HA</li><li>[Replikace mezi oblastmi](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction)</li></ul> |
| Service-Level smlouva (SLA)<br><br> Všimněte si, že SLA pro soubory a Azure NetApp Files Azure se počítá jinak. | [SLA pro soubory Azure](https://azure.microsoft.com/support/legal/sla/storage/) | [SLA pro Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp) |  
| Identity-Based ověřování a autorizace | SMB<br><ul><li>AD DS (Active Directory Domain Services)</li><li>Azure Active Directory Domain Services (Azure služba AD DS)</li></ul><br> Ověřování na základě identifikace se podporuje jenom v případě, že používáte protokol SMB. Další informace najdete v tématu [Nejčastější dotazy](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control). | SMB<br><ul><li>AD DS (Active Directory Domain Services)</li><li>Azure Active Directory Domain Services (Azure služba AD DS)</li></ul><br> Duální protokol NFS/SMB<ul><li>Přidání integrace protokolu/LDAP</li></ul><br>NFSv3/NFSv 4.1<ul><li>Přidat integraci/protokol LDAP (připravujeme)</li><li>Rozšířené skupiny NFS (přicházejících)</li></ul><br> Další informace najdete v tématu [Nejčastější dotazy](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs). |
| Šifrování | SMB<br><ul><li>Šifrování v klidovém umístění (AES 256) s klíči spravovanými zákazníky nebo Microsoftem</li><li>Šifrování protokolu Kerberos pomocí AES 256 nebo RC4-HMAC</li><li>Šifrování během přenosu</li></ul><br>NFS<br><ul><li>Šifrování v klidovém umístění (AES 256) s klíči spravovanými zákazníky nebo Microsoftem</li></ul><br>REST<br><ul><li>Šifrování v klidovém umístění (AES 256) s klíči spravovanými zákazníky nebo Microsoftem</li><li>Šifrování během přenosu</li></ul><br> Další informace najdete v tématu [zabezpečení](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols#security). | Všechny protokoly<br><ul><li>Šifrování v klidovém umístění (AES 256) pomocí klíčů spravovaných Microsoftem </li></ul><br>SYSTÉM SOUBORŮ NFS 4,1<ul><li>Šifrování při přenosu pomocí protokolu Kerberos s AES 256</li></ul><br> Další informace najdete v tématu [Nejčastější dotazy týkající se zabezpečení](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#security-faqs). |
| Možnosti přístupu | <ul><li>Internet</li><li>Zabezpečený přístup k virtuální síti</li><li>VPN Gateway</li><li>ExpressRoute</li><li>Synchronizace souborů Azure</li></ul><br> Další informace najdete v tématu [požadavky na síť](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview). | <ul><li>Zabezpečený přístup k virtuální síti</li><li>VPN Gateway</li><li>ExpressRoute</li><li>[Globální mezipaměť souborů](https://cloud.netapp.com/global-file-cache/azure)</li><li>[HPC Cache](https://docs.microsoft.com/azure/hpc-cache/hpc-cache-overview)</li></ul><br> Další informace najdete v tématu [požadavky na síť](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies). |
| Ochrana dat  | <ul><li>Přírůstkové snímky</li><li>Samoobslužné obnovení uživatele soubor/adresář</li><li>Obnovit do nového umístění</li><li>Vrácení na místě</li><li>Obnovitelné odstranění na úrovni sdílené složky</li><li>Integrace Azure Backup</li></ul><br> Další informace najdete v tématu [Azure Files vylepšuje možnosti ochrany dat](https://azure.microsoft.com/blog/azure-files-enhances-data-protection-capabilities/). | <ul><li>Snímky (255/svazek)</li><li>Samoobslužné obnovení uživatele soubor/adresář</li><li>Obnovit na nový svazek</li><li>Vrácení na místě</li><li>[Replikace mezi oblastmi](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction) (verze Public Preview)</li><li>Azure NetApp Files Backup (Preview)</li></ul><br> Další informace najdete v tématu [jak Azure NetApp Files snímky fungují](https://docs.microsoft.com/azure/azure-netapp-files/snapshots-introduction). |
| Nástroje pro migraci  | <ul><li>Azure Data Box</li><li>Synchronizace souborů Azure</li><li>Služba migrace úložiště</li><li>AzCopy</li><li>Robocopy</li></ul><br> Další informace najdete v tématu [migrace do sdílených složek Azure](https://docs.microsoft.com/azure/storage/files/storage-files-migration-overview). | <ul><li>[Globální mezipaměť souborů](https://cloud.netapp.com/global-file-cache/azure)</li><li>[CloudSync](https://cloud.netapp.com/cloud-sync-service), [XCP](https://xcp.netapp.com/)</li><li>Služba migrace úložiště</li><li>AzCopy</li><li>Robocopy</li><li>Založené na aplikacích (například HSR, Guard dat, AOAG)</li></ul> |
| Úrovně | <ul><li>Premium</li><li>Transakce optimalizovaná</li><li>Horká</li><li>Cool</li></ul><br> Další informace najdete v tématu [úrovně úložiště](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers). | <ul><li>Ultra</li><li>Premium</li><li>Standard</li></ul><br> Všechny úrovně poskytují minimální latenci v dílčím MS.<br><br> Další informace najdete v tématu požadavky na [úrovně služeb](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) a [výkon](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-considerations). |
| Ceny | [Ceny za soubory Azure](https://azure.microsoft.com/pricing/details/storage/files/) | [Ceny Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/) |

## <a name="scalability-and-performance"></a>Škálovatelnost a výkon

| Kategorie | Azure Files | Azure NetApp Files |
|---------|---------|---------|
| Minimální velikost sdílené složky nebo svazku | Premium<br><ul><li>100 GiB</li></ul><br>Standard<br><ul><li>1 GiB</li></ul> | Všechny úrovně<br><ul><li>100 GiB (minimální velikost fondu kapacit: 4 TiB)</li></ul> |
| Maximální velikost sdílené složky nebo svazku | Premium<br><ul><li>100 TiB</li></ul><br>Standard<br><ul><li>100 TiB</li></ul> | Všechny úrovně<br><ul><li>100 TiB (500-TiB limit fondu kapacit)</li></ul><br>Až 12,5 PiB na účet Azure NetApp. |
| Maximální podíl/objem IOPS | Premium<br><ul><li>Až 100 tisíc</li></ul><br>Standard<br><ul><li>Až 10 000</li></ul> | Ultra a Premium<br><ul><li>Až 450k </li></ul><br>Standard<br><ul><li>Až 320k</li></ul> |
| Maximální propustnost sdílené složky nebo svazku | Premium<br><ul><li>Až 10 GiB/s</li></ul><br>Standard<br><ul><li>Až 300 MiB/s</li></ul> | Ultra a Premium<br><ul><li>Až 4,5 GiB/s</li></ul><br>Standard<br><ul><li>Až 3.2 GiB/s</li></ul> |
| Maximální velikost souboru | Premium<br><ul><li>4 TiB</li></ul><br>Standard<br><ul><li>1 TiB</li></ul> | Všechny úrovně<br><ul><li>16 TiB</li></ul> |
| Maximální počet vstupně-výstupních operací na soubor | Premium<br><ul><li>Až 8 000</li></ul><br>Standard<br><ul><li>1 000</li></ul> | Všechny úrovně<br><ul><li>Omezení až na svazek</li></ul> |
| Maximální propustnost na soubor | Premium<br><ul><li>300 MiB/s (až 1 GiB/s s protokolem SMB vícekanálový)</li></ul><br>Standard<br><ul><li>soubory MiB 60/s</li></ul> | Všechny úrovně<br><ul><li>Omezení až na svazek</li></ul> |
| SMB Multichannel | Ano ([Preview](https://docs.microsoft.com/azure/storage/files/storage-files-smb-multichannel-performance)) | Ano |
| Latence | Minimální latence v jedné milisekundě (2 MS až 3 MS pro malé v/v) | Minimální latence v milisekundách (<1 MS pro náhodný vstup/výstup)<br><br>Další informace najdete v tématu [srovnávací testy výkonu](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux). |

Další informace o škálovatelnosti a cílech výkonu najdete v tématu [soubory](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) a [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits)Azure.

## <a name="next-steps"></a>Další kroky
* [Dokumentace ke službě Azure Files](https://docs.microsoft.com/azure/storage/files/)
* [Dokumentace k Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/)

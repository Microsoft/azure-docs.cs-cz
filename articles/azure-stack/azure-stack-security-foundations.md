---
title: "Pochopení ovládacích prvků zabezpečení zásobník Azure | Microsoft Docs"
description: "Jako správce služby Další informace o ovládací prvky zabezpečení u Azure zásobníku"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2018
ms.author: mabrigg
ms.openlocfilehash: c1d92f8f2ed9e8ab504afc65bab861e1f7bb3689
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="azure-stack-infrastructure-security-posture"></a>Azure postavení zabezpečení infrastruktury zásobníku

*Platí pro: Azure zásobníku integrované systémy*

Důležité informace o zabezpečení a předpisy patří mezi hlavní ovladačů pomocí hybridních cloudů. Azure zásobníku byl navržený pro tyto scénáře a je důležité si uvědomit ovládací prvky již zavedené při přijímání protokolů Azure.

V zásobníku Azure existují dvě vrstvy postavení zabezpečení, které společně existovat. První vrstva obsahuje zásobník Azure infrastrukturu, která přechází z hardwarové součásti úplně až Azure Resource Manager a zahrnuje správce a portály klientů. Druhá vrstva se skládá z úlohy, které klientům vytvářet, nasazovat a spravovat a jsou věci jako virtuální počítače nebo aplikační služby weby.  

## <a name="security-approach"></a>Zabezpečení přístupu
Azure zásobníku byl vytvořen s postavení zabezpečení bránit zranitelná moderními hrozbami a byl vytvořen, aby splňoval požadavky z standardů hlavní dodržování předpisů. V důsledku toho postavení zabezpečení infrastruktury Azure zásobníku je založený na dva pilíře:

 - **Předpokládejme, porušení zabezpečení.**  
Od se předpokládá, že v systému již došlo k nedodržení, soustředit na *detekovat a omezení dopad narušení* versus pouze pokusu zabránit útokům. 
 - **Posílené zabezpečení, ve výchozím nastavení.**  
Vzhledem k tomu, že infrastruktura běží na dobře definovaný hardwaru a softwaru, jsme *povolení, konfiguraci a ověření všech funkcí zabezpečení* ve výchozím nastavení.



Protože zásobník Azure je dodávána jako integrovaný systém, je definována postavení zabezpečení infrastruktury Azure zásobníku společností Microsoft. Stejně jako v Azure, klienti jsou zodpovědní za definování postavení zabezpečení zatížení klienta. Tento dokument obsahuje základní znalosti postavení zabezpečení infrastruktury Azure zásobníku.

## <a name="data-at-rest-encryption"></a>Data šifrování neaktivních dat
Všechna data zásobník Azure infrastruktury a klientů je zašifrovaná přinejmenším pomocí nástroje Bitlocker. Toto šifrování chrání před fyzické ztráty či odcizení komponent Azure zásobníku úložiště. 

## <a name="data-in-transit-encryption"></a>Data v přenosu šifrování
Součásti infrastruktury Azure zásobníku komunikovat prostřednictvím kanálů šifrována pomocí protokolu TLS 1.2. Šifrovací certifikáty jsou spravovány samostatně infrastruktury. 

Všechny koncové body externí infrastruktury, jako jsou koncové body REST nebo portálu Azure zásobníku podporovala TLS 1.2 pro zabezpečenou komunikaci. Certifikáty šifrování, buď z jiného výrobce nebo vaše podnikové certifikační autority, je třeba zadat pro tyto koncové body. 

Zatímco certifikáty podepsané svým držitelem lze použít pro tyto externí koncové body, Microsoft důrazně nedoporučuje jejich používání. 

## <a name="secret-management"></a>Tajný správy
Azure zásobníku infrastruktura používá velkého množství tajné údaje, jako jsou hesla, aby fungoval. Většina z nich automaticky otáčejí často, protože jsou účty spravované služby skupiny, které otočit každých 24 hodin.

Zbývající tajemství, které nejsou, že účty spravované služby skupiny lze otáčet ručně pomocí skriptu v privilegované koncový bod.

## <a name="code-integrity"></a>Integrity kódu
Azure zásobníku využívá nejnovější Windows Server 2016 funkce zabezpečení. Jeden z nich je Windows Defender ochranou zařízení, která zajišťuje vytvoření seznamu povolených aplikací a zajišťuje, že pouze ověřené spustí kód v rámci infrastruktury Azure zásobníku. 

Autorizovaný kód je podepsán společností Microsoft nebo výrobce OEM partnera a je zahrnutý do seznamu povolených software, který je uveden v zásadách, definovaná společností Microsoft. Jinými slovy lze provést pouze software, který byl schválen ke spuštění v infrastruktuře Azure zásobníku. Jakýkoli pokus o spuštění neoprávněný kód je blokovaný a je vygenerována auditu.

Ochrana zařízení zásady také zabrání třetích stran agentů nebo softwaru spouštět v infrastruktuře Azure zásobníku.

## <a name="credential-guard"></a>Ochrana přihlašovacích údajů
Další funkce zabezpečení systému Windows Server 2016 v zásobníku Azure je Windows Defender pověření ochrana, který se používá k ochraně před Pass-the-Hash a útoků Pass-the-Ticket pověření infrastruktury Azure zásobníku.

## <a name="antimalware"></a>Antimalware
Windows Defender antivirové je chráněný všechny komponenty v Azure zásobníku (hostitelů Hyper-V a virtuálních počítačů).

## <a name="constrained-administration-model"></a>Model omezené správy
Správa v zásobníku Azure je řízena pomocí tří vstupním bodům, každý s konkrétním účelem: 
1. [Portálu správce](azure-stack-manage-portals.md) poskytuje bod a kliknutím prostředí pro každodenní operace správy.
2. Azure Resource Manager zpřístupní všechny operace správy portálu správce prostřednictvím rozhraní REST API, používá prostředí PowerShell a rozhraní příkazového řádku Azure. 
3. Pro konkrétní operace nízké úrovně, například data center integrace nebo podporu scénářů, zásobník Azure zpřístupní koncový bod prostředí PowerShell názvem [privilegované koncový bod](azure-stack-privileged-endpoint.md). Tento koncový bod zpřístupní pouze seznam povolených adres sadu rutin a výraznou se neaudituje.

## <a name="network-controls"></a>Ovládací prvky sítě
Infrastrukturu Azure zásobníku se dodává s více vrstev sítě List(ACL) řízení přístupu. Seznamy ACL znemožníte neoprávněný přístup k součásti infrastruktury a infrastruktury komunikace se jenom cesty, které jsou požadovány pro fungování omezit. 

Seznamy ACL sítě se vynucují v tři vrstvy:
1.  Začátek přepínače Rack
2.  Softwarově definované sítě
3.  Brány firewall operačního systému hostitele a virtuálního počítače 



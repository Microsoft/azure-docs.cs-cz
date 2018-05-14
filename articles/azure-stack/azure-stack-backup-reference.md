---
title: Referenční dokumentace Azure zásobníku infrastruktura zálohování služba | Microsoft Docs
description: Tento článek obsahuje referenčního materiálu pro službu Azure zásobníku infrastruktura zálohování.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/20/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 608f3043e0e4b851820274ca743cbc44d1c8c0f1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="infrastructure-backup-service-reference"></a>Odkaz na službu zálohování infrastruktury

## <a name="azure-backup-infrastructure"></a>Infrastrukturu Azure backup

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Azure zásobníku se skládá z mnoha služby, které obsahují na portálu Azure Resource Manager, a zkušenosti správu infrastruktury. Rozhraní pro správu zařízení jako této Azure zásobníku se zaměřuje na snížení složitosti vystavený operátor řešení.

Infrastruktura zálohování slouží k internalize složitosti zálohování a obnovení dat pro služby infrastruktury, zajištění operátory zaměřit na řešení pro správu a údržbu SLA pro uživatele.

Export dat zálohování do externí sdílené složky je nezbytnou k tomu ukládání záloh ve stejném systému. Správce nutnosti externí sdílené složky poskytuje flexibilitu určit, kam se mají ukládat data na základě existující BC nebo zotavení po Havárii zásad společnosti. 

### <a name="infrastructure-backup-components"></a>Součásti infrastruktury zálohování

Infrastruktura zálohování zahrnuje následující součásti:

 - **Infrastruktura zálohování řadiče**  
 Adaptér zálohování infrastruktury je vytvořena s a se nachází v každé zásobníku cloudu Azure.
 - **Poskytovatel prostředků zálohování**  
 Zálohování poskytovatele prostředků (zálohování RP) se skládá z uživatelské rozhraní a aplikací programu rozhraní (API) s vystavení základních funkcí zálohování Azure zásobníku infrastruktury.

#### <a name="infrastructure-backup-controller"></a>Infrastruktura zálohování řadiče

Záložní řadič infrastruktury je Service Fabric získá instanci služby pro cloudové služby Azure zásobníku. Zálohování prostředky jsou vytvořené na místní úrovni a zachycení služby oblast dat ze Správce prostředků Azure AD, certifikační Autority, CRP, SRP, NRP, KeyVault, RBAC. 

### <a name="backup-resource-provider"></a>Poskytovatel prostředků zálohování

Zálohování poskytovatele prostředků uvede uživatelského rozhraní na portálu Azure zásobníku pro základní konfigurace a seznam zálohování prostředky. Operátor můžete provádět následující operace v uživatelském rozhraní:

 - Povolení zálohování poprvé zadáním externím úložišti, přihlašovací údaje a šifrovací klíč
 - Zobrazení dokončit vytvořit zálohování a stav prostředků v části Vytvoření
 - Změnit umístění úložiště, kde zálohování řadiče umístí zálohování dat
 - Upravit přihlašovací údaje, které řadič zálohování používá pro přístup k externím úložišti
 - Upravit šifrovací klíč, který řadič zálohování používá k šifrování záloh 


## <a name="backup-controller-requirements"></a>Požadavky na zálohování řadiče

Tato část popisuje požadavky na důležité pro zálohování infrastruktury. Doporučujeme, abyste si prošli informace pečlivě před povolení zálohování Azure zásobníku instance a pak zpátky na ni odkazuje podle potřeby během nasazení a další operace.

Požadavky na patří:

  - **Požadavky na software** – popisuje podporované úložiště umístění a pokyny k dimenzování. 
  - **Požadavky na síťovou** – popisuje požadavky sítě pro umístění jiného úložiště.  

### <a name="software-requirements"></a>Požadavky na software

#### <a name="supported-storage-locations"></a>Umístění úložiště podporované

| Umístění úložiště                                                                 | Podrobnosti                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sdílené složky SMB hostované na paměťovém zařízení v důvěryhodné síti prostředí | Sdílená složka SMB, ve stejném datovém centru, které se nasadí zásobník Azure nebo v jiném datovém centru. Více instancí zásobník Azure můžete použít stejné sdílené složky. |
| Sdílené složce SMB na Azure                                                          | Není aktuálně podporováno.                                                                                                                                 |
| Úložiště objektů BLOB v Azure                                                            | Není aktuálně podporováno.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Podporované verze protokolu SMB

| SMB | Verze |
|-----|---------|
| SMB | 3.x     |

#### <a name="storage-location-sizing"></a>Nastavení velikosti umístění úložiště 

Infrastruktura zálohování řadiče bude zálohovat data na vyžádání. Doporučuje se zálohování alespoň dvakrát den a udržování maximálně sedm dní záloh. 

| Škálování prostředí | Předpokládané velikost zálohy | Celková velikost požadovaného místa |
|-------------------|--------------------------|--------------------------------|
| 4 12 uzly        | 10 GB                     | 140 GB                          |

### <a name="network-requirements"></a>Síťové požadavky
| Umístění úložiště                                                                 | Podrobnosti                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sdílené složky SMB hostované na paměťovém zařízení v důvěryhodné síti prostředí | Port 445 je povinný, pokud instance Azure zásobníku se nachází v prostředí s bránou firewall. Infrastruktura zálohování řadiče zahájí připojení k souborovému serveru SMB přes port 445. |
| Pokud chcete použít plně kvalifikovaný název domény souborového serveru, musí jít přeložit z období název             |                                                                                                                                                                                         |

> [!Note]  
> Žádné příchozí porty musí být otevřen.


## <a name="infrastructure-backup-limits"></a>Omezení infrastruktury zálohování

Zvážit tyto limity při plánování, nasadit a provozovat vaše instance Microsoft Azure zásobníku. Následující tabulka popisuje tyto limity.

### <a name="infrastructure-backup-limits"></a>Omezení infrastruktury zálohování
| Identifikátor omezení                                                 | Omezení        | Komentáře                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Typ zálohy                                                      | Pouze úplná    | Infrastruktura zálohování řadiče podporuje pouze úplné zálohy. Nejsou přírůstkové zálohy podporovány.                                          |
| Naplánovaných záloh                                                | Ruční pouze  | Zálohování řadiče aktuálně podporuje jenom zálohování na vyžádání                                                                                 |
| Maximální souběžných úloh zálohování                                   | 1            | Každou instanci zálohování řadiče je podporován pouze jeden aktivní úloha zálohování.                                                                  |
| Konfigurace přepínače sítě                                     | Není v oboru | Správce musí zálohovat konfiguraci přepínače sítě pomocí nástroje pro výrobce OEM. Najdete v dokumentaci k Azure zásobníku poskytnutý dodavatelem každý výrobce OEM. |
| Životní cyklus hostitele hardwaru                                          | Není v oboru | Správce musí zálohovat hostitele životního cyklu hardwaru pomocí nástroje pro výrobce OEM. Najdete v dokumentaci k Azure zásobníku poskytnutý dodavatelem každý výrobce OEM.      |
| Maximální počet sdílených složek                                    | 1            | Pouze jeden sdílené složky lze použít k uložení zálohy dat                                                                                        |
| Zálohování aplikační služby, funkce, SQL, databáze mysql prostředků zprostředkovatele dat | Není v oboru | Naleznete pokyny publikována pro nasazení a správa přidanou hodnotou RPs vytvořené společností Microsoft.                                                  |
| Zprostředkovatelé prostředků zálohování třetích stran                              | Není v oboru | Naleznete pokyny publikována pro nasazení a správa přidanou hodnotou RPs od jiných výrobců třetích stran.                                          |

## <a name="next-steps"></a>Další postup

 - Další informace o službě zálohování infrastruktury najdete v tématu [zálohování a data obnovení pro zásobník Azure pomocí služby Backup infrastruktury](azure-stack-backup-infrastructure-backup.md).
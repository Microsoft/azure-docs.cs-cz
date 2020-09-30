---
title: Co je nového v Azure NetApp Files | Microsoft Docs
description: Obsahuje souhrn nejnovějších nových funkcí a vylepšení Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: beb1cc9957f7b43bc6ad9b9b6ee6c7707eb0c2d6
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91571562"
---
# <a name="whats-new-in-azure-netapp-files"></a>Co je nového v Azure NetApp Files

Azure NetApp Files se pravidelně aktualizují. Tento článek obsahuje souhrn nejnovějších nových funkcí a vylepšení. 

## <a name="september-2020"></a>Září 2020

* [Azure NetApp Files replikace mezi oblastmi](cross-region-replication-introduction.md) (Public Preview)

  Azure NetApp Files teď podporuje replikaci mezi jednotlivými oblastmi. Díky této nové funkci pro zotavení po havárii můžete své Azure NetApp Files svazky replikovat z jedné oblasti Azure do jiné díky rychlému a nákladově efektivnímu způsobu ochrany vašich dat před nepředvídatelnými místními chybami. Replikace Azure NetApp Files mezi oblastmi využije technologii® NetApp SnapMirror; v komprimovaném, efektivním formátu se přes síť odesílají jenom změněné bloky. Tato proprietární technologie minimalizuje množství dat potřebných k replikaci napříč různými oblastmi, takže šetří náklady na přenos dat. Také zkracuje čas replikace, takže můžete dosáhnout menšího cíle bodu obnovení (RPO).

* [Ruční fond kapacit technologie QoS](manage-manual-qos-capacity-pool.md) (Preview)  

    V manuálním fondu kapacity QoS můžete přiřadit kapacitu a propustnost pro svazek nezávisle. Celková propustnost všech svazků vytvořených pomocí manuálního fondu kapacity QoS je omezená celkovou propustností fondu. Je určena kombinací velikosti fondu a propustnosti na úrovni služby. Alternativně může být [typ QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) fondu kapacity automaticky (automaticky), což je výchozí nastavení. Ve fondu kapacity auto QoS se propustnost automaticky přiřazuje svazkům ve fondu, a to úměrně k kvótě velikosti přiřazené ke svazkům.

* [Podepisování LDAP](azure-netapp-files-create-volumes-smb.md) (Preview)   

    Azure NetApp Files teď podporuje podepisování LDAP pro zabezpečené vyhledávání LDAP mezi službou Azure NetApp Files a uživatelem zadanými Active Directory Domain Services řadiči domény. Tato funkce je aktuálně ve verzi Preview.

* [Šifrování AES pro ověřování AD](azure-netapp-files-create-volumes-smb.md) (Preview)

    Azure NetApp Files teď podporuje šifrování AES pro připojení LDAP k řadiči domény, aby se povolilo šifrování AES pro svazek SMB. Tato funkce je aktuálně ve verzi Preview. 

* Nové [metriky](azure-netapp-files-metrics.md):   

    * Nové metriky svazků: 
        * *Velikost přiděleného svazku*: zřízená velikost svazku
    * Metriky nového fondu: 
        * *Velikost přiděleného fondu*: zřízená velikost fondu 
        * *Celková velikost snímku pro fond*: součet velikosti snímku ze všech svazků ve fondu

## <a name="july-2020"></a>Červenec 2020

* [Svazek s duálním protokolem (NFSv3 a SMB)](create-volumes-dual-protocol.md)

    Nyní můžete vytvořit Azure NetApp Files svazek, který umožní souběžný přístup k duálnímu protokolu (NFS v3 a SMB) s podporou mapování uživatelů LDAP. Tato funkce umožňuje případy použití, kdy může být úloha založená na systému Linux, která generuje a ukládá data ve Azure NetApp Filesm svazku. Současně musí vaši zaměstnanci pomocí klientů a softwaru se systémem Windows analyzovat nově vytvořená data ze stejného Azure NetApp Filesho svazku. Současná funkce přístupu s duálním protokolem eliminuje nutnost kopírování dat generovaných úloh do samostatného svazku s jiným protokolem po následné analýze, uložení nákladů na úložiště a provozním časem. Tato funkce je bezplatná (cena za běžné [Azure NetApp Files úložiště](https://azure.microsoft.com/pricing/details/netapp/) je stále platná) a je všeobecně dostupná. Další informace najdete v [Souběžné dokumentaci k přístupu pomocí duálního protokolu](create-volumes-dual-protocol.MD).

* [Šifrování protokolu Kerberos v 4.1 systému souborů NFS v přenosu](configure-kerberos-encryption.MD)

    Azure NetApp Files teď podporuje šifrování klienta NFS v režimech Kerberos (krb5, Krb5i a krb5p) s šifrováním AES-256 a poskytuje další zabezpečení dat. Tato funkce je bezplatná (cena za běžné [Azure NetApp Files úložiště](https://azure.microsoft.com/pricing/details/netapp/) je stále platná) a je všeobecně dostupná. Další informace najdete v [dokumentaci k šifrování protokolu Kerberos pro systém souborů NFS v 4.1](configure-kerberos-encryption.MD).

* [Změna úrovně dynamické služby Volume Service](dynamic-change-volume-service-level.MD)

    Flexibilita cloudového příslibůu ve výdajích IT. Teď můžete změnit úroveň služby existujícího svazku Azure NetApp Files tím, že ho přesunete do jiného fondu kapacity, který využívá požadovanou úroveň služby pro daný svazek. Tato místní změna na úrovni služby pro svazek nevyžaduje migraci dat. Nemá vliv na přístup roviny dat ke svazku. Existující svazek můžete změnit tak, aby používal vyšší úroveň služby pro lepší výkon, nebo použít nižší úroveň služby pro optimalizaci nákladů. Tato funkce je bezplatná (cena za běžné [Azure NetApp Files úložiště](https://azure.microsoft.com/pricing/details/netapp/) je stále platná) a je v současnosti ve verzi Public Preview. Verzi Preview můžete zaregistrovat podle pokynů v [dokumentaci ke změně úrovně služby dynamického svazku](dynamic-change-volume-service-level.md).

* [Zásady Snímkování svazků](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) (Preview) 

    Azure NetApp Files vám umožní vytvořit snímky svazků v čase. Nyní můžete vytvořit zásadu snímku, která bude Azure NetApp Files automaticky vytvářet snímky svazků podle zvolené frekvence. Snímky můžete naplánovat tak, aby se provedly v hodinových, denních, týdenních nebo měsíčních cyklech. Můžete také určit maximální počet snímků, které budou zachovány v rámci zásad snímku. Tato funkce je bezplatná (cena za běžné [Azure NetApp Files úložiště](https://azure.microsoft.com/pricing/details/netapp/) je stále platná) a je aktuálně ve verzi Preview. Pomocí [dokumentace k zásadám Snímkování svazků](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)se můžete zaregistrovat pro náhled funkce.

* [Zásada exportu přístupu ke kořenovému adresáři NFS](azure-netapp-files-configure-export-policy.md)

    Azure NetApp Files teď umožňuje určit, jestli má mít kořenový účet přístup ke svazku. 

* [Skrýt cestu k snímku](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)

    Azure NetApp Files teď umožňuje určit, jestli se uživatel může `.snapshot` na připojeném svazku zobrazit a přistupovat k adresáři (klienti NFS) nebo `~snapshot` složce (klienti SMB).

## <a name="may-2020"></a>Květen 2020

* [Uživatelé zásad zálohování](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection) (Preview)

    Azure NetApp Files vám umožní zahrnout další účty, které vyžadují zvýšená oprávnění k účtu počítače vytvořenému pro použití s Azure NetApp Files. U zadaných účtů bude povoleno změnit oprávnění systému souborů NTFS na úrovni souboru nebo složky. Můžete například zadat účet neprivilegované služby, který se používá k migraci dat do sdílené složky SMB v Azure NetApp Files. Funkce uživatelé zásad zálohování je teď ve verzi Preview.

## <a name="next-steps"></a>Další kroky
* [Co je služba Azure NetApp Files](azure-netapp-files-introduction.md)
* [Vysvětlení hierarchie úložiště služby Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) 

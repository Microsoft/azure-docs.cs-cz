---
title: Azure VMware Solution by CloudSimple – model oprávnění privátního cloudu
description: Popisuje model, skupiny a kategorie cloudového privátního cloudu.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 28c4dc7831f97d66eb4d47f08e640344d5cca0d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014942"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>CloudSimple Private Cloud model oprávnění VMware vCenter

CloudSimple si zachovává úplný přístup správce k prostředí privátního cloudu. Každému zákazníkovi CloudSimple jsou udělena dostatečná oprávnění správce, aby mohli nasadit a spravovat virtuální počítače ve svém prostředí.  V případě potřeby můžete dočasně eskalovat oprávnění k provádění funkcí správy.

## <a name="cloud-owner"></a>Vlastník cloudu

Když vytvoříte privátní cloud, vytvoří se v doméně vCenter jednotné přihlášení uživatel **CloudOwner** s přístupem **ke službě Cloud-Owner-Role** pro správu objektů v privátním cloudu. Tento uživatel může také nastavit další [zdroje identity vCenter](set-vcenter-identity.md)a ostatní uživatele do centra vcenter privátního cloudu.

> [!NOTE]
> Výchozí uživatel pro cloudsimple privátní cloud vCenter je cloudowner@cloudsimple.local při vytvoření privátního cloudu.

## <a name="user-groups"></a>Skupiny uživatelů

Skupina s názvem **Cloud-Owner-Group** se vytvoří během nasazení privátního cloudu. Uživatelé v této skupině mohou spravovat různé části prostředí vSphere v privátním cloudu. Tato skupina je automaticky udělena oprávnění **Cloud-Owner-Role** a uživatel **CloudOwner** je přidán jako člen této skupiny.  CloudSimple vytvoří další skupiny s omezenými oprávněními pro snadnou správu.  Do těchto předem vytvořených skupin můžete přidat libovolného uživatele a níže definovaná oprávnění jsou automaticky přiřazena uživatelům ve skupinách.

### <a name="pre-created-groups"></a>Předem vytvořené skupiny

| Název skupiny | Účel | Role |
| -------- | ------- | ------ |
| Cloud-Vlastník-Skupina | Členové této skupiny mají oprávnění správce vprogramu Privátního cloudu. | [Role vlastníka cloudu](#cloud-owner-role) |
| Cloud-Global-Cluster-Admin-Group | Členové této skupiny mají oprávnění správce v clusteru private cloud vCenter | [Role cloud-cluster-admin](#cloud-cluster-admin-role) |
| Cloud-Global-Storage-Admin-Group | Členové této skupiny mohou spravovat úložiště v programu Private Cloud vCenter. | [Role cloudového úložiště a správy](#cloud-storage-admin-role) |
| Cloud-Global-Network-Admin-Group | Členové této skupiny mohou spravovat skupiny síťových a distribuovaných portů v programu Private Cloud vCenter. | [Cloud-Network-Admin-Role](#cloud-network-admin-role) |
| Cloud-Global-VM-Admin-Group | Členové této skupiny mohou spravovat virtuální počítače v programu Private Cloud vCenter. | [Role cloud-vm-admin](#cloud-vm-admin-role) |

Chcete-li jednotlivým uživatelům udělit oprávnění ke správě privátního cloudu, vytvořte uživatelské účty, které se přidávají do příslušných skupin.

> [!CAUTION]
> Noví uživatelé musí být přidáni pouze do *skupiny Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* *nebo, Cloud-Global-VM-Admin-Group*.  Uživatelé připřidání do *skupiny Administrators* budou automaticky odebráni.  K přihlášení k webovému uživatelskému rozhraní vSphere je nutné přidat pouze účty služeb a účty služeb *administrators* a účty služeb.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Seznam oprávnění programu vCenter pro výchozí role

### <a name="cloud-owner-role"></a>Role vlastníka cloudu

| **Kategorie** | **Privilege** |
|----------|-----------|
| **Upozornění** | Potvrzení upozornění <br> Vytvoření alarmu <br> Zakázat akci alarmu <br> Změna budíku <br> Odebrání alarmu <br> Nastavení stavu budíku |
| **Oprávnění** | Změnit oprávnění |
| **Knihovna obsahu** | Přidání položky knihovny <br> Vytvořit místní knihovnu <br> Vytvoření odebírané knihovny <br> Odstranit položku knihovny <br> Odstranit místní knihovnu <br> Odstranit odebírané knihovny <br> Stažení souborů <br> Položka knihovny vystěhovávat <br> Knihovna přihlášená <br> Import skladování <br> Informace o předplatném sondy <br> Čtení úložiště <br> Synchronizace položky knihovny <br> Synchronizovat odebírané knihovny <br> Typ introspekce <br> Aktualizovat nastavení konfigurace <br> Aktualizace souborů <br> Aktualizovat knihovnu <br> Aktualizovat položku knihovny <br> Aktualizace místní knihovny <br> Aktualizovat odebírané knihovny <br> Zobrazit nastavení konfigurace |
| **Kryptografické operace** | Přidání disku <br> Klonování <br> Dešifrování <br> Přímý přístup <br> Šifrování <br> Šifrovat nové <br> Správa služby KMS <br> Správa zásad šifrování <br> Správa klíčů <br> Migrace <br> Rešifrování <br> Zaregistrovat virtuální hod <br> Zaregistrovat hostitele |
| **skupina dvPort** | Vytvořit <br> Odstranění <br> Modify <br> Operace politiky <br> Provoz oboru |
| **Úložiště dat** | Přidělení místa <br> Procházet úložiště dat <br> Konfigurace úložiště dat <br> Operace se soubory nižší úrovně <br> Přesunout úložiště dat <br> Odebrání úložiště dat <br> Odebrat soubor <br> Přejmenovat úložiště dat <br> Aktualizace souborů virtuálních strojů <br> Aktualizace metadat virtuálního počítače |
| **Manažer agenta ESX** | Config <br> Modify <br> Zobrazení |
| **Linka** | Rozšíření registru <br> Zrušit registraci rozšíření <br> Rozšíření aktualizace |
| **Externí statistiky zprostředkovatele**| Zaregistrovat <br> Zrušit registraci <br> Aktualizace |
| **Složka** | Vytvořit složku <br> Odstranit složku <br> Přesunout složku <br> Přejmenovat složku |
| **Globální** | Zrušit úkol <br> Plánování kapacity <br> Diagnostika <br> Zakázat metody <br> Povolit metody <br> Globální značka <br> Stav <br> Licence <br> Událost protokolu <br> Správa vlastních atributů <br> Proxy server <br> Akce skriptu <br> Správci služeb <br> Nastavit vlastní atribut <br> Systémová značka |
| **Zprostředkovatel aktualizace stavu** | Zaregistrovat <br> Zrušit registraci <br> Aktualizace |
| **Konfigurace > hostitele** | Konfigurace oddílu úložiště |
| **Inventář > hostitele** | Změna clusteru |
| **vSphere Tagging** | Přiřadit nebo zrušit přiřazení značky vSphere <br> Vytvořit značku vSphere <br> Vytvořit kategorii značek vSphere <br> Odstranit značku vSphere <br> Odstranit kategorii značek vSphere <br> Upravit značku vSphere <br> Upravit kategorii značek vSphere <br> Změnit pole UsedBy pro kategorii <br> Upravit pole Usedby pro značku |
| **Síť** | Přiřadit síť <br> Konfigurace <br> Přesunutí sítě <br> Odebrat |
| **Výkon** | Úprava intervalů |
| **Profil hostitele** | Zobrazení |
| **Zdrojů** | Použít doporučení <br> Přiřazení aplikace vApp do fondu zdrojů <br> Přiřazení virtuálního počítače do fondu prostředků <br> Vytvořit fond zdrojů <br> Migrace vypnutý virtuální počítač <br> Migrace na virtuálním počítači <br> Upravit fond zdrojů <br> Přesunout fond zdrojů <br> Dotaz vMotion <br> Odebrat fond zdrojů <br> Přejmenovat fond zdrojů |
| **Naplánovaná úloha** | Vytvoření úkolů <br> Upravit úkol <br> Odebrat úkol <br> Spustit úlohu |
| **Relace** | Zosobnit uživatele <br> Zpráva <br> Ověřit relaci <br> Zobrazení a zastavení relací |
| **Cluster úložiště dat** | Konfigurace clusteru úložiště dat |
| **Úložiště řízené profily** | Aktualizace úložiště řízeného profily <br> Zobrazení úložiště řízené profily |
| **Zobrazení úložiště** | Konfigurace služby <br> Zobrazení |
| **Úlohy** | Vytvořit úkol <br> Aktualizovat úkol |
| **Přenosová služba**| Správa <br> Monitorování |
| **vApp** | Přidání virtuálního počítače <br> Přiřazení fondu zdrojů <br> Přiřadit vApp <br> Klonování <br> Vytvořit <br> Odstranění <br> Export <br> Import <br> Přesunout <br> Napájení vypnuto <br> Zapnutí <br> Přejmenovat <br> Suspend <br> Zrušit registraci <br> Zobrazit prostředí OVF <br> konfigurace aplikace vApp <br> konfigurace instance vApp <br> vApp managedBy konfigurace <br> konfigurace prostředků vApp |
| **Zásady vrm** | Zásady vrm dotazu <br> Aktualizovat zásady VRM |
| **Konfigurace > virtuálního počítače** | Přidání existujícího disku <br> Přidat nový disk <br> Přidání nebo odebrání zařízení <br> Upřesnit <br> Změna počtu procesorů <br> Změnit zdroj <br> Konfigurovat managedBy <br> Sledování změn disku <br> Zapůjčení disku <br> Zobrazit nastavení připojení <br> Rozšíření virtuálního disku <br> Hostitelské zařízení USB <br> Memory (Paměť) <br> Změna nastavení zařízení <br> Kompatibilita s odolností proti chybám dotazu <br> Dotaz na nepoužité soubory <br> Nezpracované zařízení <br> Znovu načíst z cesty <br> Odebrat disk <br> Přejmenovat <br> Obnovit informace o hostu <br> Nastavení poznámky <br> Nastavení <br> Umístění odkládacího souboru <br> Přepnout nadřazenou položku vyklopit <br> Odemknutí virtuálního počítače <br> Upgrade kompatibility virtuálních strojů |
| **Operace virtuálního počítače > hosta** | Změna aliasu operace hosta <br> Dotaz aliasu operace hosta <br> Úpravy provozu hosta <br> Spuštění operačního programu hosta <br> Dotazy na operace hosta |
| **Interakce > virtuálního počítače** | Odpověď na otázku <br> Operace zálohování na virtuálním počítači <br> Konfigurace média CD <br> Konfigurace disketových médií <br> Interakce konzoly <br> Vytvoření snímku obrazovky <br> Defragmentace všech disků <br> Připojení zařízení <br> Přetažení <br> Správa hostovaného operačního systému podle Rozhraní VIX API <br> Vstříkněte kódy skenování USB HID <br> Pozastavení nebo zrušení pozastavení <br> Provádění operací vymazání nebo zmenšení <br> Napájení vypnuto <br> Zapnutí <br> Záznam relace ve virtuálním počítači <br> Replay relace na virtuálním počítači <br> Resetovat <br> Obnovit odolnost proti chybám <br> Suspend <br> Pozastavit odolnost proti chybám <br> Testovací převzetí služeb při selhání <br> Testovací restart sekundárního virtuálního počítače <br> Vypnout odolnost proti chybám <br> Zapnout odolnost proti chybám <br> Instalace nástrojů VMware |
| **Inventář > virtuálního počítače** | Vytvořit z existujících <br> Vytvořit nový <br> Přesunout <br> Zaregistrovat <br> Odebrat <br> Zrušit registraci |
| **Zřizování > virtuálních strojů** | Povolit přístup k disku <br> Povolit přístup k souborům <br> Povolit přístup k disku jen pro čtení <br> Povolit stahování virtuálního počítače <br> Povolení nahrávání souborů virtuálních strojů <br> Šablona klonování <br> Klonovat virtuální stroj <br> Vytvoření šablony z virtuálního počítače <br> Přizpůsobení <br> Nasazení šablony <br> Označit jako šablonu <br> Označit jako virtuální počítač <br> Upravit specifikaci vlastního nastavení <br> Propagace disků <br> Přečtěte si specifikace vlastního nastavení |
| **Konfigurace > služby virtuálního počítače** | Allow notifications <br> Povolit dotazování globálních oznámení událostí <br> Správa konfigurací služeb <br> Změna konfigurace služby <br> Konfigurace služby dotazů <br> Čtení konfigurace služby |
| **Správa > snímků virtuálního počítače** | Vytvoření snímku <br> Odebrat snímek <br> Přejmenovat snímek <br> Vrátit se ke snímku |
| **Replikace virtuálního počítače > vSphere** | Konfigurace replikace <br> Správa replikace <br> Monitorování replikace |
| **vSlužba** | Vytvořit závislost <br> Zničit závislost <br> Změna konfigurace závislosti <br> Aktualizovat závislost |

### <a name="cloud-cluster-admin-role"></a>Role cloud-cluster-admin

| **Kategorie** | **Privilege** |
|----------|-----------|
| **Úložiště dat** | Přidělení místa <br> Procházet úložiště dat <br> Konfigurace úložiště dat <br> Operace se soubory nižší úrovně <br> Odebrání úložiště dat <br> Přejmenovat úložiště dat <br> Aktualizace souborů virtuálních strojů <br> Aktualizace metadat virtuálního počítače |
| **Složka** | Vytvořit složku <br> Odstranit složku <br> Přesunout složku <br> Přejmenovat složku |
| **Konfigurace > hostitele**  | Konfigurace oddílu úložiště |
| **vSphere Tagging** | Přiřadit nebo zrušit přiřazení značky vSphere <br> Vytvořit značku vSphere <br> Vytvořit kategorii značek vSphere <br> Odstranit značku vSphere <br> Odstranit kategorii značek vSphere <br> Upravit značku vSphere <br> Upravit kategorii značek vSphere <br> Změnit pole UsedBy pro kategorii <br> Upravit pole Usedby pro značku |
| **Síť** | Přiřadit síť |
| **Zdrojů** | Použít doporučení <br> Přiřazení aplikace vApp do fondu zdrojů <br> Přiřazení virtuálního počítače do fondu prostředků <br> Vytvořit fond zdrojů <br> Migrace vypnutý virtuální počítač <br> Migrace na virtuálním počítači <br> Upravit fond zdrojů <br> Přesunout fond zdrojů <br> Dotaz vMotion <br> Odebrat fond zdrojů <br> Přejmenovat fond zdrojů |
| **vApp** | Přidání virtuálního počítače <br> Přiřazení fondu zdrojů <br> Přiřadit vApp <br> Klonování <br> Vytvořit <br> Odstranění <br> Export <br> Import <br> Přesunout <br> Napájení vypnuto <br> Zapnutí <br> Přejmenovat <br> Suspend <br> Zrušit registraci <br> Zobrazit prostředí OVF <br> konfigurace aplikace vApp <br> konfigurace instance vApp <br> vApp managedBy konfigurace <br> konfigurace prostředků vApp |
| **Zásady vrm** | Zásady vrm dotazu <br> Aktualizovat zásady VRM |
| **Konfigurace > virtuálního počítače** | Přidání existujícího disku <br> Přidat nový disk <br> Přidání nebo odebrání zařízení <br> Upřesnit <br> Změna počtu procesorů <br> Změnit zdroj <br> Konfigurovat managedBy <br> Sledování změn disku <br> Zapůjčení disku <br> Zobrazit nastavení připojení <br> Rozšíření virtuálního disku <br> Hostitelské zařízení USB <br> Memory (Paměť) <br> Změna nastavení zařízení <br> Kompatibilita s odolností proti chybám dotazu <br> Dotaz na nepoužité soubory <br> Nezpracované zařízení <br> Znovu načíst z cesty <br> Odebrat disk <br> Přejmenovat <br> Obnovit informace o hostu <br> Nastavení poznámky <br> Nastavení <br> Umístění odkládacího souboru <br> Přepnout nadřazenou položku vyklopit <br> Odemknutí virtuálního počítače <br> Upgrade kompatibility virtuálních strojů |
| **Operace virtuálního počítače > hosta** | Změna aliasu operace hosta <br> Dotaz aliasu operace hosta <br> Úpravy provozu hosta <br> Spuštění operačního programu hosta <br> Dotazy na operace hosta |
| **Interakce > virtuálního počítače** | Odpověď na otázku <br> Operace zálohování na virtuálním počítači <br> Konfigurace média CD <br> Konfigurace disketových médií <br> Interakce konzoly <br> Vytvoření snímku obrazovky <br> Defragmentace všech disků <br> Připojení zařízení <br> Přetažení <br> Správa hostovaného operačního systému podle Rozhraní VIX API <br> Vstříkněte kódy skenování USB HID <br> Pozastavení nebo zrušení pozastavení <br> Provádění operací vymazání nebo zmenšení <br> Napájení vypnuto <br> Zapnutí <br> Záznam relace ve virtuálním počítači <br> Replay relace na virtuálním počítači <br> Resetovat <br> Obnovit odolnost proti chybám <br> Suspend <br> Pozastavit odolnost proti chybám <br> Testovací převzetí služeb při selhání <br> Testovací restart sekundárního virtuálního počítače <br> Vypnout odolnost proti chybám <br> Zapnout odolnost proti chybám <br> Instalace nástrojů VMware
| **Inventář > virtuálního počítače** | Vytvořit z existujících <br> Vytvořit nový <br> Přesunout <br> Zaregistrovat <br> Odebrat <br> Zrušit registraci |
| **Zřizování > virtuálních strojů** | Povolit přístup k disku <br> Povolit přístup k souborům <br> Povolit přístup k disku jen pro čtení <br> Povolit stahování virtuálního počítače <br> Povolení nahrávání souborů virtuálních strojů <br> Šablona klonování <br> Klonovat virtuální stroj <br> Vytvoření šablony z virtuálního počítače <br> Přizpůsobení <br> Nasazení šablony <br> Označit jako šablonu <br> Označit jako virtuální počítač <br> Upravit specifikaci vlastního nastavení <br> Propagace disků  <br> Přečtěte si specifikace vlastního nastavení |
| **Konfigurace > služby virtuálního počítače** | Allow notifications <br> Povolit dotazování globálních oznámení událostí <br> Správa konfigurací služeb <br> Změna konfigurace služby <br> Konfigurace služby dotazů <br> Čtení konfigurace služby
| **Správa > snímků virtuálního počítače** | Vytvoření snímku <br> Odebrat snímek <br> Přejmenovat snímek <br> Vrátit se ke snímku |
| **Replikace virtuálního počítače > vSphere** | Konfigurace replikace <br> Správa replikace <br> Monitorování replikace |
| **vSlužba** | Vytvořit závislost <br> Zničit závislost <br> Změna konfigurace závislosti <br> Aktualizovat závislost |

### <a name="cloud-storage-admin-role"></a>Role cloudového úložiště a správy

| **Kategorie** | **Privilege** |
|----------|-----------|
| **Úložiště dat** | Přidělení místa <br> Procházet úložiště dat <br> Konfigurace úložiště dat <br> Operace se soubory nižší úrovně <br> Odebrání úložiště dat <br> Přejmenovat úložiště dat <br> Aktualizace souborů virtuálních strojů <br> Aktualizace metadat virtuálního počítače |
| **Konfigurace > hostitele** | Konfigurace oddílu úložiště |
| **Cluster úložiště dat** | Konfigurace clusteru úložiště dat |
| **Úložiště řízené profily** | Aktualizace úložiště řízeného profily <br> Zobrazení úložiště řízené profily |
| **Zobrazení úložiště** | Konfigurace služby <br> Zobrazení |

### <a name="cloud-network-admin-role"></a>Cloud-Network-Admin-Role

| **Kategorie** | **Privilege** |
|----------|-----------|
| **skupina dvPort** | Vytvořit <br> Odstranění <br> Modify <br> Operace politiky <br> Provoz oboru |
| **Síť** | Přiřadit síť <br> Konfigurace <br> Přesunutí sítě <br> Odebrat |
| **Konfigurace > virtuálního počítače** | Změna nastavení zařízení |

### <a name="cloud-vm-admin-role"></a>Role cloud-vm-admin

| **Kategorie** | **Privilege** |
|----------|-----------|
| **Úložiště dat** | Přidělení místa <br> Procházet úložiště dat |
| **Síť** | Přiřadit síť |
| **Zdrojů** | Přiřazení virtuálního počítače do fondu prostředků <br> Migrace vypnutý virtuální počítač <br> Migrace na virtuálním počítači
| **vApp** | Export <br> Import |
| **Konfigurace > virtuálního počítače** | Přidání existujícího disku <br> Přidat nový disk <br> Přidání nebo odebrání zařízení <br> Upřesnit <br> Změna počtu procesorů <br> Změnit zdroj <br> Konfigurovat managedBy <br> Sledování změn disku <br> Zapůjčení disku <br> Zobrazit nastavení připojení <br> Rozšíření virtuálního disku <br> Hostitelské zařízení USB <br> Memory (Paměť) <br> Změna nastavení zařízení <br> Kompatibilita s odolností proti chybám dotazu <br> Dotaz na nepoužité soubory <br> Nezpracované zařízení <br> Znovu načíst z cesty <br> Odebrat disk <br> Přejmenovat <br> Obnovit informace o hostu <br> Nastavení poznámky <br> Nastavení <br> Umístění odkládacího souboru <br> Přepnout nadřazenou položku vyklopit <br> Odemknutí virtuálního počítače <br> Upgrade kompatibility virtuálních strojů |
| **Operace virtuálního počítače >hosta** | Změna aliasu operace hosta <br> Dotaz aliasu operace hosta <br> Úpravy provozu hosta <br> Spuštění operačního programu hosta <br> Dotazy na operace hosta    |
| **Interakce >virtuálního počítače** | Odpověď na otázku <br> Operace zálohování na virtuálním počítači <br> Konfigurace média CD <br> Konfigurace disketových médií <br> Interakce konzoly <br> Vytvoření snímku obrazovky <br> Defragmentace všech disků <br> Připojení zařízení <br> Přetažení <br> Správa hostovaného operačního systému podle Rozhraní VIX API <br> Vstříkněte kódy skenování USB HID <br> Pozastavení nebo zrušení pozastavení <br> Provádění operací vymazání nebo zmenšení <br> Napájení vypnuto <br> Zapnutí <br> Záznam relace ve virtuálním počítači <br> Replay relace na virtuálním počítači <br> Resetovat <br> Obnovit odolnost proti chybám <br> Suspend <br> Pozastavit odolnost proti chybám <br> Testovací převzetí služeb při selhání <br> Testovací restart sekundárního virtuálního počítače <br> Vypnout odolnost proti chybám <br> Zapnout odolnost proti chybám <br> Instalace nástrojů VMware |
| **Inventář >virtuálních strojů** | Vytvořit z existujících <br> Vytvořit nový <br> Přesunout <br> Zaregistrovat <br> Odebrat <br> Zrušit registraci |
| **Zřizování >virtuálních strojů** | Povolit přístup k disku <br> Povolit přístup k souborům <br> Povolit přístup k disku jen pro čtení <br> Povolit stahování virtuálního počítače <br> Povolení nahrávání souborů virtuálních strojů <br> Šablona klonování <br> Klonovat virtuální stroj <br> Vytvoření šablony z virtuálního počítače <br> Přizpůsobení <br> Nasazení šablony <br> Označit jako šablonu <br> Označit jako virtuální počítač <br> Upravit specifikaci vlastního nastavení <br> Propagace disků <br> Přečtěte si specifikace vlastního nastavení |
| **Konfigurace virtuálního počítače >služby** | Allow notifications <br> Povolit dotazování globálních oznámení událostí <br> Správa konfigurací služeb <br> Změna konfigurace služby <br> Konfigurace služby dotazů <br> Čtení konfigurace služby
| **Správa >snímků virtuálního počítače** | Vytvoření snímku <br> Odebrat snímek <br> Přejmenovat snímek <br> Vrátit se ke snímku |
| **Virtuální počítač >replikaci vSphere** | Konfigurace replikace <br> Správa replikace <br> Monitorování replikace |
| **vSlužba** | Vytvořit závislost <br> Zničit závislost <br> Změna konfigurace závislosti <br> Aktualizovat závislost |

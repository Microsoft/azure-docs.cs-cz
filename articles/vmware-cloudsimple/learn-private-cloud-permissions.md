---
title: Řešení Azure VMware podle CloudSimple – model oprávnění privátního cloudu
description: Popisuje model oprávnění privátního cloudu CloudSimple, skupiny a kategorie.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1c8cfeda008955006f2fbad1df58c8047bd36541
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898041"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>Model oprávnění privátního cloudu CloudSimple VMware vCenter

CloudSimple zachovává úplný přístup správce k prostředí privátního cloudu. Každý zákazník CloudSimple má dostatečná oprávnění správce, aby bylo možné nasadit a spravovat virtuální počítače ve svém prostředí.  V případě potřeby můžete dočasně zvýšit vaše oprávnění k provádění funkcí správy.

## <a name="cloud-owner"></a>Vlastník cloudu

Když vytvoříte privátní cloud, vytvoří se uživatel **CloudOwner** v doméně vCenter s jednou Sign-On doménou a bude mít přístup ke správě objektů v privátním cloudu pomocí **role Cloud-vlastník** . Tento uživatel také může nastavit další [zdroje identity vCenter](set-vcenter-identity.md)a další uživatele na Server vCenter privátního cloudu.

> [!NOTE]
> Výchozí uživatel pro CloudSimple privátního cloudu vCenter je cloudowner@cloudsimple.local při vytvoření privátního cloudu.

## <a name="user-groups"></a>Skupiny uživatelů

Při nasazení privátního cloudu se vytvoří skupina s názvem **Cloud-vlastník-skupina** . Uživatelé v této skupině můžou spravovat různé části prostředí vSphere v privátním cloudu. Tato skupina automaticky udělí oprávnění  **role vlastníka cloudu** a uživatel  **CloudOwner** se přidá jako člen této skupiny.  CloudSimple vytvoří další skupiny s omezenými oprávněními pro snadné řízení.  Do těchto předem vytvořených skupin můžete přidat libovolného uživatele a níže definovaná oprávnění jsou automaticky přiřazena uživatelům ve skupinách.

### <a name="pre-created-groups"></a>Předem vytvořené skupiny

| Název skupiny | Účel | Role |
| -------- | ------- | ------ |
| Cloud – vlastník – skupina | Členové této skupiny mají oprávnění ke správě privátního cloudu vCenter. | [Vlastník cloudu – role](#cloud-owner-role) |
| Cloud-Global-cluster-admin-Group | Členové této skupiny mají oprávnění správce v clusteru vCenter privátního cloudu. | [Cloud-cluster-admin-role](#cloud-cluster-admin-role) |
| Cloud – globální úložiště – admin-Group | Členové této skupiny můžou spravovat úložiště v privátním cloudu vCenter. | [Cloud – úložiště – role správce](#cloud-storage-admin-role) |
| Cloud-Global-Network-admin-Group | Členové této skupiny mohou spravovat sítě a distribuované skupiny portů v rámci serveru vCenter privátního cloudu. | [Cloud-síť-správce-role](#cloud-network-admin-role) |
| Cloud – Global-VM-admin-Group | Členové této skupiny můžou spravovat virtuální počítače v privátním cloudu vCenter. | [Cloud-VM-admin-role](#cloud-vm-admin-role) |

Chcete-li udělit individuálním uživatelům oprávnění ke správě privátního cloudu, vytvořte uživatelské účty, které chcete přidat do příslušných skupin.

> [!CAUTION]
> Noví uživatelé musí být přidáni pouze *ke skupině* Cloud-Owner-Group *-Global-cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin* -Group nebo, *Cloud-Global-VM-admin-Group*.  Uživatelé přidaní do skupiny *správců* se automaticky odeberou.  Pouze účty služeb musí být přidány do skupiny *Administrators* a účty služeb nesmí být použity pro přihlášení k WEBOVÉmu uživatelskému rozhraní vSphere.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Seznam oprávnění vCenter pro výchozí role

### <a name="cloud-owner-role"></a>Vlastník cloudu – role

| **Kategorie** | **Privilege** |
|----------|-----------|
| **Upozornění** | Potvrzení upozornění <br> Vytvořit alarm <br> Zakázat akci alarmu <br> Upravit alarm <br> Odebrat alarm <br> Nastavit stav alarmu |
| **Oprávnění** | Upravit oprávnění |
| **Knihovna obsahu** | Přidat položku knihovny <br> Vytvořit místní knihovnu <br> Vytvořit předplacenou knihovnu <br> Odstranit položku knihovny <br> Odstranit místní knihovnu <br> Odstranit předplacenou knihovnu <br> Stažení souborů <br> Vyřazení položky knihovny <br> Vyřazení odebírané knihovny <br> Import úložiště <br> Informace o předplatném testu <br> Čtení úložiště <br> Synchronizovat položku knihovny <br> Synchronizovat předplacenou knihovnu <br> Typ introspekce <br> Aktualizovat nastavení konfigurace <br> Soubory aktualizací <br> Aktualizovat knihovnu <br> Aktualizovat položku knihovny <br> Aktualizovat místní knihovnu <br> Aktualizace odebírané knihovny <br> Zobrazit nastavení konfigurace |
| **Kryptografické operace** | Přidat disk <br> Klonování <br> Dešifrování <br> Přímý přístup <br> Šifrování <br> Zašifrovat nové <br> Správa služby správy klíčů <br> Správa zásad šifrování <br> Správa klíčů <br> Migrate <br> Znovu zašifrovaný <br> Registrovat virtuální počítač <br> Registrovat hostitele |
| **Skupina dvPort** | Vytvořit <br> Odstranit <br> Modify <br> Operace zásad <br> Operace oboru |
| **Úložiště dat** | Přidělit místo <br> Procházet úložiště dat <br> Konfigurace úložiště dat <br> Operace se soubory na nízké úrovni <br> Přesunout úložiště dat <br> Odebrat úložiště dat <br> Odebrat soubor <br> Přejmenovat úložiště dat <br> Aktualizace souborů virtuálního počítače <br> Aktualizace metadat virtuálního počítače |
| **Správce agenta ESX** | Config <br> Modify <br> Zobrazení |
| **Linka** | Registrovat rozšíření <br> Zrušit registraci rozšíření <br> Aktualizovat rozšíření |
| **Externí poskytovatel statistik**| Registrovat <br> Zrušit registraci <br> Aktualizace |
| **Složka** | Vytvořit složku <br> Odstranit složku <br> Přesunout složku <br> Přejmenovat složku |
| **Globální** | Zrušit úlohu <br> Plánování kapacity <br> Diagnostika <br> Zakázat metody <br> Povolit metody <br> Globální značka <br> Zdravotnictví <br> Licenses <br> Událost protokolu <br> Správa vlastních atributů <br> Proxy server <br> Akce skriptu <br> Manažeři služeb <br> Nastavit vlastní atribut <br> Systémová značka |
| **Poskytovatel aktualizace stavu** | Registrovat <br> Zrušit registraci <br> Aktualizace |
| **Konfigurace > hostitele** | Konfigurace oddílů úložiště |
| **Inventář > hostitele** | Upravit cluster |
| **Označování vSphere** | Přiřadit nebo zrušit přiřazení značky vSphere <br> Vytvořit značku vSphere <br> Vytvořit kategorii značek vSphere <br> Odstranit značku vSphere <br> Odstranit kategorii značek vSphere <br> Upravit značku vSphere <br> Upravit kategorii značek vSphere <br> Upravit pole UsedBy pro kategorii <br> Upravit pole UsedBy pro značku |
| **Síť** | Přiřadit síť <br> Konfigurace <br> Přesunout síť <br> Odebrat |
| **Výkon** | Upravit intervaly |
| **Profil hostitele** | Zobrazení |
| **Prostředek** | Použít doporučení <br> Přiřazení vApp ke fondu zdrojů <br> Přiřadit virtuální počítač k fondu zdrojů <br> Vytvořit fond zdrojů <br> Migrace vypnutého virtuálního počítače <br> Migrace zapnutá na virtuálním počítači <br> Upravit fond zdrojů <br> Přesunout fond zdrojů <br> VMotion dotazu <br> Odebrat fond zdrojů <br> Přejmenovat fond zdrojů |
| **Naplánovaná úloha** | Vytváření úloh <br> Upravit úlohu <br> Odebrat úlohu <br> Spustit úlohu |
| **Relace** | Zosobnit uživatele <br> Zpráva <br> Ověřit relaci <br> Zobrazení a zastavení relací |
| **Cluster úložiště dat** | Konfigurace clusteru úložiště dat |
| **Úložiště řízené profilem** | Aktualizace úložiště řízené profilem <br> Zobrazení úložiště řízené profilem |
| **Zobrazení úložišť** | Konfigurace služby <br> Zobrazení |
| **Úlohy** | Vytvořit úlohu <br> Aktualizovat úkol |
| **Služba Transfer Service**| Spravovat <br> Monitor |
| **vApp** | Přidat virtuální počítač <br> Přiřadit fond zdrojů <br> Přiřadit vApp <br> Klonování <br> Vytvořit <br> Odstranit <br> Export <br> Import <br> Přesunout <br> Napájení vypnuto <br> Zapnout <br> přejmenování <br> Suspend <br> Zrušit registraci <br> Zobrazit prostředí OVF <br> Konfigurace aplikace vApp <br> konfigurace instance vApp <br> Konfigurace vApp managedBy <br> Konfigurace prostředků vApp |
| **VRMPolicy** | VRMPolicy dotazu <br> Aktualizovat VRMPolicy |
| **Konfigurace > virtuálních počítačů** | Přidat existující disk <br> Přidat nový disk <br> Přidat nebo odebrat zařízení <br> Pokročilý <br> Změna počtu PROCESORů <br> Změnit prostředek <br> Konfigurace managedBy <br> Sledování změn disků <br> Zapůjčení disku <br> Zobrazit nastavení připojení <br> Zvětšit virtuální disk <br> Hostitelské zařízení USB <br> Memory (Paměť) <br> Úprava nastavení zařízení <br> Kompatibilita dotazů na odolnost proti chybám <br> Dotazování nevlastněných souborů <br> Nezpracované zařízení <br> Znovu načíst z cesty <br> Odebrat disk <br> přejmenování <br> Resetovat informace o hostovi <br> Nastavit poznámku <br> Nastavení <br> Umístění swapfile <br> Přepnout nadřazenou větev <br> Odemknout virtuální počítač <br> Upgrade kompatibility virtuálních počítačů |
| **Operace hosta > virtuálních počítačů** | Úprava aliasu operace hosta <br> Dotaz na alias operace hosta <br> Úpravy operací hosta <br> Spuštění programu operace hosta <br> Dotazy na operace hosta |
| **Interakce > virtuálních počítačů** | Odpověď na otázku <br> Operace zálohování na virtuálním počítači <br> Konfigurace média CD <br> Konfigurace disketových médií <br> Interakce konzoly <br> Vytvořit snímek obrazovky <br> Defragmentovat všechny disky <br> Připojení zařízení <br> Přetažení <br> Správa hostovaného operačního systému pomocí rozhraní VIX API <br> Vložení kódů kontroly USB HID <br> Pozastavit nebo pozastavit <br> Provádění operací vymazání nebo zmenšení <br> Napájení vypnuto <br> Zapnout <br> Zaznamenat relaci na virtuálním počítači <br> Znovu spustit relaci na virtuálním počítači <br> Resetovat <br> Obnovit odolnost proti chybám <br> Suspend <br> Pozastavit odolnost proti chybám <br> Testovací převzetí služeb při selhání <br> Sekundární virtuální počítač pro restartování testu <br> Vypnout odolnost proti chybám <br> Zapnout odolnost proti chybám <br> Instalace nástrojů VMware |
| **Inventář > virtuálních počítačů** | Vytvořit z existujících <br> Vytvořit nový <br> Přesunout <br> Registrovat <br> Odebrat <br> Zrušit registraci |
| **Zřizování > virtuálních počítačů** | Povolení přístupu k disku <br> Povolení přístupu k souborům <br> Povolení přístupu k disku jen pro čtení <br> Povolení stahování virtuálního počítače <br> Povolení nahrávání souborů virtuálního počítače <br> Klonovat šablonu <br> Klonovat virtuální počítač <br> Vytvořit šablonu z virtuálního počítače <br> Přizpůsobení <br> Nasazení šablony <br> Označit jako šablonu <br> Označit jako virtuální počítač <br> Upravit specifikaci přizpůsobení <br> Zvýšit úroveň disků <br> Čtení specifikací pro přizpůsobení |
| **Konfigurace služby > virtuálních počítačů** | Allow notifications <br> Povolení cyklického dotazování na globální oznamování událostí <br> Správa konfigurací služby <br> Úprava konfigurace služby <br> Konfigurace dotazů služby <br> Přečíst konfiguraci služby |
| **Správa snímků > virtuálních počítačů** | Vytvoření snímku <br> Odebrat snímek <br> Přejmenovat snímek <br> Vrátit se ke snímku |
| **Replikace virtuálního počítače > vSphere** | Konfigurace replikace <br> Správa replikace <br> Monitorování replikace |
| **vService** | Vytvořit závislost <br> Zničit závislost <br> Překonfigurujte konfiguraci závislostí. <br> Aktualizovat závislost |

### <a name="cloud-cluster-admin-role"></a>Cloud-cluster-admin-role

| **Kategorie** | **Privilege** |
|----------|-----------|
| **Úložiště dat** | Přidělit místo <br> Procházet úložiště dat <br> Konfigurace úložiště dat <br> Operace se soubory na nízké úrovni <br> Odebrat úložiště dat <br> Přejmenovat úložiště dat <br> Aktualizace souborů virtuálního počítače <br> Aktualizace metadat virtuálního počítače |
| **Složka** | Vytvořit složku <br> Odstranit složku <br> Přesunout složku <br> Přejmenovat složku |
| **Konfigurace > hostitele**  | Konfigurace oddílů úložiště |
| **Označování vSphere** | Přiřadit nebo zrušit přiřazení značky vSphere <br> Vytvořit značku vSphere <br> Vytvořit kategorii značek vSphere <br> Odstranit značku vSphere <br> Odstranit kategorii značek vSphere <br> Upravit značku vSphere <br> Upravit kategorii značek vSphere <br> Upravit pole UsedBy pro kategorii <br> Upravit pole UsedBy pro značku |
| **Síť** | Přiřadit síť |
| **Prostředek** | Použít doporučení <br> Přiřazení vApp ke fondu zdrojů <br> Přiřadit virtuální počítač k fondu zdrojů <br> Vytvořit fond zdrojů <br> Migrace vypnutého virtuálního počítače <br> Migrace zapnutá na virtuálním počítači <br> Upravit fond zdrojů <br> Přesunout fond zdrojů <br> VMotion dotazu <br> Odebrat fond zdrojů <br> Přejmenovat fond zdrojů |
| **vApp** | Přidat virtuální počítač <br> Přiřadit fond zdrojů <br> Přiřadit vApp <br> Klonování <br> Vytvořit <br> Odstranit <br> Export <br> Import <br> Přesunout <br> Napájení vypnuto <br> Zapnout <br> přejmenování <br> Suspend <br> Zrušit registraci <br> Zobrazit prostředí OVF <br> Konfigurace aplikace vApp <br> konfigurace instance vApp <br> Konfigurace vApp managedBy <br> Konfigurace prostředků vApp |
| **VRMPolicy** | VRMPolicy dotazu <br> Aktualizovat VRMPolicy |
| **Konfigurace > virtuálních počítačů** | Přidat existující disk <br> Přidat nový disk <br> Přidat nebo odebrat zařízení <br> Pokročilý <br> Změna počtu PROCESORů <br> Změnit prostředek <br> Konfigurace managedBy <br> Sledování změn disků <br> Zapůjčení disku <br> Zobrazit nastavení připojení <br> Zvětšit virtuální disk <br> Hostitelské zařízení USB <br> Memory (Paměť) <br> Úprava nastavení zařízení <br> Kompatibilita dotazů na odolnost proti chybám <br> Dotazování nevlastněných souborů <br> Nezpracované zařízení <br> Znovu načíst z cesty <br> Odebrat disk <br> přejmenování <br> Resetovat informace o hostovi <br> Nastavit poznámku <br> Nastavení <br> Umístění swapfile <br> Přepnout nadřazenou větev <br> Odemknout virtuální počítač <br> Upgrade kompatibility virtuálních počítačů |
| **Operace hosta > virtuálních počítačů** | Úprava aliasu operace hosta <br> Dotaz na alias operace hosta <br> Úpravy operací hosta <br> Spuštění programu operace hosta <br> Dotazy na operace hosta |
| **Interakce > virtuálních počítačů** | Odpověď na otázku <br> Operace zálohování na virtuálním počítači <br> Konfigurace média CD <br> Konfigurace disketových médií <br> Interakce konzoly <br> Vytvořit snímek obrazovky <br> Defragmentovat všechny disky <br> Připojení zařízení <br> Přetažení <br> Správa hostovaného operačního systému pomocí rozhraní VIX API <br> Vložení kódů kontroly USB HID <br> Pozastavit nebo pozastavit <br> Provádění operací vymazání nebo zmenšení <br> Napájení vypnuto <br> Zapnout <br> Zaznamenat relaci na virtuálním počítači <br> Znovu spustit relaci na virtuálním počítači <br> Resetovat <br> Obnovit odolnost proti chybám <br> Suspend <br> Pozastavit odolnost proti chybám <br> Testovací převzetí služeb při selhání <br> Sekundární virtuální počítač pro restartování testu <br> Vypnout odolnost proti chybám <br> Zapnout odolnost proti chybám <br> Instalace nástrojů VMware
| **Inventář > virtuálních počítačů** | Vytvořit z existujících <br> Vytvořit nový <br> Přesunout <br> Registrovat <br> Odebrat <br> Zrušit registraci |
| **Zřizování > virtuálních počítačů** | Povolení přístupu k disku <br> Povolení přístupu k souborům <br> Povolení přístupu k disku jen pro čtení <br> Povolení stahování virtuálního počítače <br> Povolení nahrávání souborů virtuálního počítače <br> Klonovat šablonu <br> Klonovat virtuální počítač <br> Vytvořit šablonu z virtuálního počítače <br> Přizpůsobení <br> Nasazení šablony <br> Označit jako šablonu <br> Označit jako virtuální počítač <br> Upravit specifikaci přizpůsobení <br> Zvýšit úroveň disků  <br> Čtení specifikací pro přizpůsobení |
| **Konfigurace služby > virtuálních počítačů** | Allow notifications <br> Povolení cyklického dotazování na globální oznamování událostí <br> Správa konfigurací služby <br> Úprava konfigurace služby <br> Konfigurace dotazů služby <br> Přečíst konfiguraci služby
| **Správa snímků > virtuálních počítačů** | Vytvoření snímku <br> Odebrat snímek <br> Přejmenovat snímek <br> Vrátit se ke snímku |
| **Replikace virtuálního počítače > vSphere** | Konfigurace replikace <br> Správa replikace <br> Monitorování replikace |
| **vService** | Vytvořit závislost <br> Zničit závislost <br> Překonfigurujte konfiguraci závislostí. <br> Aktualizovat závislost |

### <a name="cloud-storage-admin-role"></a>Cloud – úložiště – role správce

| **Kategorie** | **Privilege** |
|----------|-----------|
| **Úložiště dat** | Přidělit místo <br> Procházet úložiště dat <br> Konfigurace úložiště dat <br> Operace se soubory na nízké úrovni <br> Odebrat úložiště dat <br> Přejmenovat úložiště dat <br> Aktualizace souborů virtuálního počítače <br> Aktualizace metadat virtuálního počítače |
| **Konfigurace > hostitele** | Konfigurace oddílů úložiště |
| **Cluster úložiště dat** | Konfigurace clusteru úložiště dat |
| **Úložiště řízené profilem** | Aktualizace úložiště řízené profilem <br> Zobrazení úložiště řízené profilem |
| **Zobrazení úložišť** | Konfigurace služby <br> Zobrazení |

### <a name="cloud-network-admin-role"></a>Cloud-síť-správce-role

| **Kategorie** | **Privilege** |
|----------|-----------|
| **Skupina dvPort** | Vytvořit <br> Odstranit <br> Modify <br> Operace zásad <br> Operace oboru |
| **Síť** | Přiřadit síť <br> Konfigurace <br> Přesunout síť <br> Odebrat |
| **Konfigurace > virtuálních počítačů** | Úprava nastavení zařízení |

### <a name="cloud-vm-admin-role"></a>Cloud-VM-admin-role

| **Kategorie** | **Privilege** |
|----------|-----------|
| **Úložiště dat** | Přidělit místo <br> Procházet úložiště dat |
| **Síť** | Přiřadit síť |
| **Prostředek** | Přiřadit virtuální počítač k fondu zdrojů <br> Migrace vypnutého virtuálního počítače <br> Migrace zapnutá na virtuálním počítači
| **vApp** | Export <br> Import |
| **Konfigurace > virtuálních počítačů** | Přidat existující disk <br> Přidat nový disk <br> Přidat nebo odebrat zařízení <br> Pokročilý <br> Změna počtu PROCESORů <br> Změnit prostředek <br> Konfigurace managedBy <br> Sledování změn disků <br> Zapůjčení disku <br> Zobrazit nastavení připojení <br> Zvětšit virtuální disk <br> Hostitelské zařízení USB <br> Memory (Paměť) <br> Úprava nastavení zařízení <br> Kompatibilita dotazů na odolnost proti chybám <br> Dotazování nevlastněných souborů <br> Nezpracované zařízení <br> Znovu načíst z cesty <br> Odebrat disk <br> přejmenování <br> Resetovat informace o hostovi <br> Nastavit poznámku <br> Nastavení <br> Umístění swapfile <br> Přepnout nadřazenou větev <br> Odemknout virtuální počítač <br> Upgrade kompatibility virtuálních počítačů |
| **Operace hosta >virtuálních počítačů** | Úprava aliasu operace hosta <br> Dotaz na alias operace hosta <br> Úpravy operací hosta <br> Spuštění programu operace hosta <br> Dotazy na operace hosta    |
| **Interakce >virtuálních počítačů** | Odpověď na otázku <br> Operace zálohování na virtuálním počítači <br> Konfigurace média CD <br> Konfigurace disketových médií <br> Interakce konzoly <br> Vytvořit snímek obrazovky <br> Defragmentovat všechny disky <br> Připojení zařízení <br> Přetažení <br> Správa hostovaného operačního systému pomocí rozhraní VIX API <br> Vložení kódů kontroly USB HID <br> Pozastavit nebo pozastavit <br> Provádění operací vymazání nebo zmenšení <br> Napájení vypnuto <br> Zapnout <br> Zaznamenat relaci na virtuálním počítači <br> Znovu spustit relaci na virtuálním počítači <br> Resetovat <br> Obnovit odolnost proti chybám <br> Suspend <br> Pozastavit odolnost proti chybám <br> Testovací převzetí služeb při selhání <br> Sekundární virtuální počítač pro restartování testu <br> Vypnout odolnost proti chybám <br> Zapnout odolnost proti chybám <br> Instalace nástrojů VMware |
| **Inventář >virtuálních počítačů** | Vytvořit z existujících <br> Vytvořit nový <br> Přesunout <br> Registrovat <br> Odebrat <br> Zrušit registraci |
| **Zřizování >virtuálních počítačů** | Povolení přístupu k disku <br> Povolení přístupu k souborům <br> Povolení přístupu k disku jen pro čtení <br> Povolení stahování virtuálního počítače <br> Povolení nahrávání souborů virtuálního počítače <br> Klonovat šablonu <br> Klonovat virtuální počítač <br> Vytvořit šablonu z virtuálního počítače <br> Přizpůsobení <br> Nasazení šablony <br> Označit jako šablonu <br> Označit jako virtuální počítač <br> Upravit specifikaci přizpůsobení <br> Zvýšit úroveň disků <br> Čtení specifikací pro přizpůsobení |
| **Konfigurace služby >virtuálních počítačů** | Allow notifications <br> Povolení cyklického dotazování na globální oznamování událostí <br> Správa konfigurací služby <br> Úprava konfigurace služby <br> Konfigurace dotazů služby <br> Přečíst konfiguraci služby
| **Správa snímků >virtuálních počítačů** | Vytvoření snímku <br> Odebrat snímek <br> Přejmenovat snímek <br> Vrátit se ke snímku |
| **Replikace virtuálního počítače >vSphere** | Konfigurace replikace <br> Správa replikace <br> Monitorování replikace |
| **vService** | Vytvořit závislost <br> Zničit závislost <br> Překonfigurujte konfiguraci závislostí. <br> Aktualizovat závislost |

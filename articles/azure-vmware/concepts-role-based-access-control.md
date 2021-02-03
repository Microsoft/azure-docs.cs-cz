---
title: Koncepty – vSphere řízení přístupu na základě role (vSphere RBAC)
description: Přečtěte si o klíčových schopnostech vSphere řízení přístupu na základě rolí pro řešení Azure VMware.
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: 6f5c30b070b010f312d038d8286be994039c56f8
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509244"
---
# <a name="vsphere-role-based-access-control-vsphere-rbac-for-azure-vmware-solution"></a>vSphere řízení přístupu na základě role (vSphere RBAC) pro řešení Azure VMware

V řešení Azure VMware má vCenter integrovaný místní uživatel s názvem cloudadmin a přiřazený k předdefinované roli CloudAdmin. Místní uživatel cloudadmin se používá k nastavení uživatelů ve službě AD. Obecně platí, že role CloudAdmin vytváří a spravuje úlohy v privátním cloudu. V řešení Azure VMware má role CloudAdmin oprávnění vCenter, která se liší od jiných cloudových řešení VMware.     

> [!NOTE]
> Řešení Azure VMware v současnosti nenabízí vlastní role na vCenter nebo na portálu řešení Azure VMware. 

V případě místního nasazení vCenter a ESXi má správce přístup k administrator@vsphere.local účtu vCenter. Můžou mít taky přiřazené další uživatele nebo skupiny služby Active Directory (AD). 

V nasazení řešení Azure VMware nemá správce přístup k uživatelskému účtu správce. Ale můžou k roli CloudAdmin v vCenter přiřadit uživatele a skupiny služby AD.  

Uživatel privátního cloudu nemá přístup ke službě a nemůže konfigurovat konkrétní součásti pro správu podporované a spravované společností Microsoft. Například clustery, hostitelé, úložiště dat a distribuované virtuální přepínače.

## <a name="azure-vmware-solution-cloudadmin-role-on-vcenter"></a>Role CloudAdmin řešení Azure VMware na serveru vCenter

Můžete zobrazit oprávnění udělená roli CloudAdmin Azure VMware pro řešení Azure VMware v rámci vašeho privátního cloudu vCenter.

1. Přihlaste se ke klientovi SDDC vSphere a přejděte do **nabídky**  >  **Správa**.
1. V části **Access Control** vyberte **role**.
1. V seznamu rolí vyberte **CloudAdmin** a pak vyberte **oprávnění**. 

   :::image type="content" source="media/role-based-access-control-cloudadmin-privileges.png" alt-text="Jak zobrazit oprávnění role CloudAdmin v klientovi vSphere":::

Role CloudAdmin v řešení Azure VMware má následující oprávnění pro vCenter. Podrobné vysvětlení jednotlivých oprávnění najdete v [dokumentaci k produktu VMware](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html) .

| Privilege | Description |
| --------- | ----------- |
| **Upozornění** | Potvrzení upozornění<br />Vytvořit alarm<br />Zakázat akci alarmu<br />Upravit alarm<br />Odebrat alarm<br />Nastavit stav alarmu |
| **Oprávnění** | Upravit oprávnění |
| **Knihovna obsahu** | Přidat položku knihovny<br />Vytvoření odběru publikované knihovny<br />Vytvořit místní knihovnu<br />Vytvořit předplacenou knihovnu<br />Odstranit položku knihovny<br />Odstranit místní knihovnu<br />Odstranit předplacenou knihovnu<br />Odstranění předplatného publikované knihovny<br />Stažení souborů<br />Vyřazení položek knihovny<br />Vyřazení odebírané knihovny<br />Import úložiště<br />Informace o předplatném testu<br />Publikování položky knihovny pro její předplatitele<br />Publikování knihovny pro své předplatitele<br />Čtení úložiště<br />Synchronizovat položku knihovny<br />Synchronizovat předplacenou knihovnu<br />Typ introspekce<br />Aktualizovat nastavení konfigurace<br />Soubory aktualizací<br />Aktualizovat knihovnu<br />Aktualizovat položku knihovny<br />Aktualizovat místní knihovnu<br />Aktualizace odebírané knihovny<br />Aktualizace předplatného publikované knihovny<br />Zobrazit nastavení konfigurace |
| **Kryptografické operace** | Přímý přístup |
| **Úložiště dat** | Přidělit místo<br />Procházet úložiště dat<br />Konfigurace úložiště dat<br />Operace se soubory na nízké úrovni<br />Odebrat soubory<br />Aktualizace metadat virtuálního počítače |
| **Složka** | Vytvořit složku<br />Odstranit složku<br />Přesunout složku<br />Přejmenovat složku |
| **Globální** | Zrušit úlohu<br />Globální značka<br />Zdravotnictví<br />Událost protokolu<br />Správa vlastních atributů<br />Manažeři služeb<br />Nastavit vlastní atribut<br />Systémová značka |
| **Hostitel** | vSphere replikace<br />&#160;&#160;&#160;&#160;spravovat replikaci |
| **označování vSphere** | Přiřadit a zrušit přiřazení značky vSphere<br />Vytvořit značku vSphere<br />Vytvořit kategorii značek vSphere<br />Odstranit značku vSphere<br />Odstranit kategorii značek vSphere<br />Upravit značku vSphere<br />Upravit kategorii značek vSphere<br />Upravit pole UsedBy pro kategorii<br />Upravit pole UsedBy pro značku |
| **Síť** | Přiřadit síť |
| **Prostředek** | Použít doporučení<br />Přiřazení vApp ke fondu zdrojů<br />Přiřadit virtuální počítač k fondu zdrojů<br />Vytvořit fond zdrojů<br />Migrace vypnutého virtuálního počítače<br />Migrace zapnutá na virtuálním počítači<br />Upravit fond zdrojů<br />Přesunout fond zdrojů<br />VMotion dotazu<br />Odebrat fond zdrojů<br />Přejmenovat fond zdrojů |
| **Naplánovaná úloha** | Vytvořit úlohu<br />Upravit úlohu<br />Odebrat úlohu<br />Spustit úlohu |
| **Relace** | Zpráva<br />Ověřit relaci |
| **Profil** | Zobrazení úložiště řízené profilem |
| **Zobrazení úložiště** | Zobrazení |
| **vApp** | Přidat virtuální počítač<br />Přiřadit fond zdrojů<br />Přiřadit vApp<br />Klonování<br />Vytvořit<br />Odstranit<br />Export<br />Import<br />Přesunout<br />Napájení vypnuto<br />Zapnout<br />přejmenování<br />Suspend<br />Zrušit registraci<br />Zobrazit prostředí OVF<br />Konfigurace aplikace vApp<br />konfigurace instance vApp<br />Konfigurace vApp managedBy<br />Konfigurace prostředků vApp |
| **Virtuální počítač** | Změnit konfiguraci<br />&#160;&#160;&#160;&#160;získání zapůjčení disku<br />&#160;&#160;&#160;&#160;přidat existující disk<br />&#160;&#160;&#160;&#160;přidat nový disk<br />&#160;&#160;&#160;&#160;přidat nebo odebrat zařízení<br />&#160;&#160;&#160;&#160;Pokročilá konfigurace<br />&#160;&#160;&#160;&#160;Změna počtu PROCESORů<br />&#160;&#160;&#160;&#160;změnit paměť<br />&#160;&#160;&#160;&#160;změnit nastavení<br />&#160;&#160;&#160;&#160;změnit umístění swapfile<br />Prostředek změny &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;Konfigurace hostitelského zařízení USB<br />&#160;&#160;&#160;&#160;konfigurace nezpracovaného zařízení<br />&#160;&#160;&#160;&#160;konfigurace managedBy<br />&#160;&#160;&#160;&#160;zobrazení nastavení připojení<br />&#160;&#160;&#160;&#160;– zvětšit virtuální disk<br />&#160;&#160;&#160;&#160;upravit nastavení zařízení<br />Kompatibilita &#160;&#160;&#160;&#160;dotazů na odolnost proti chybám<br />&#160;&#160;&#160;&#160;nevlastněných souborů dotazů<br />Opětovné načtení &#160;&#160;&#160;&#160;z cest<br />&#160;&#160;&#160;&#160;odebrat disk<br />Přejmenování &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;resetovat informace o hostovi<br />&#160;&#160;&#160;&#160;nastavit poznámku<br />&#160;&#160;&#160;&#160;přepínání sledování změn disků<br />Přepínací tlačítko pro rozvětvení &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;upgrade kompatibility virtuálních počítačů<br />Upravit inventář<br />&#160;&#160;&#160;&#160;vytvořit z existujícího<br />&#160;&#160;&#160;&#160;vytvořit nový<br />&#160;&#160;&#160;&#160;přesun<br />&#160;&#160;&#160;&#160;registraci<br />&#160;&#160;&#160;&#160;odebrat<br />&#160;&#160;&#160;&#160;zrušit registraci<br />Operace hosta<br />&#160;&#160;&#160;&#160;úpravy aliasu operace hosta<br />&#160;&#160;&#160;&#160;dotaz na alias operace hosta<br />&#160;&#160;&#160;&#160;úpravy operací hosta<br />&#160;&#160;&#160;&#160;provádění programu operace hosta<br />&#160;&#160;&#160;&#160;dotazy na operace hosta<br />Interakce<br />Odpověď na &#160;&#160;&#160;&#160;otázku<br />&#160;&#160;&#160;&#160;operaci zálohování na virtuálním počítači<br />&#160;&#160;&#160;&#160;nakonfigurovat médium CD<br />&#160;&#160;&#160;&#160;konfigurace disketových médií<br />&#160;&#160;&#160;&#160;připojit zařízení<br />Interakce konzoly &#160;&#160;&#160;&#160;<br />Vytvoření snímku obrazovky &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;defragmentovat všechny disky<br />&#160;&#160;&#160;&#160;přetahování<br />&#160;&#160;&#160;&#160;správu hostovaného operačního systému pomocí rozhraní API pro VIX<br />&#160;&#160;&#160;&#160;vložení kódů kontroly USB HID<br />&#160;&#160;&#160;&#160;instalaci nástrojů VMware<br />Pozastavení nebo zastavení &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;provádění operací vymazání nebo zmenšení<br />Vypínání &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;zapnutí<br />&#160;&#160;&#160;&#160;relace záznamu na virtuálním počítači<br />&#160;&#160;&#160;&#160;relace přehrání na virtuálním počítači<br />Pozastavení &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;pozastavit odolnost proti chybám<br />&#160;&#160;&#160;&#160;testovací převzetí služeb při selhání<br />&#160;&#160;&#160;&#160;sekundární virtuální počítač pro restartování testu<br />&#160;&#160;&#160;&#160;vypnout odolnost proti chybám<br />&#160;&#160;&#160;&#160;zapnout odolnost proti chybám<br />Zřizování<br />&#160;&#160;&#160;&#160;povolení přístupu k disku<br />&#160;&#160;&#160;&#160;povolení přístupu k souborům<br />&#160;&#160;&#160;&#160;povolí přístup k disku jen pro čtení.<br />&#160;&#160;&#160;&#160;povolení stahování virtuálního počítače<br />&#160;&#160;&#160;&#160;šablonu klonování<br />&#160;&#160;&#160;&#160;klonovat virtuální počítač<br />&#160;&#160;&#160;&#160;vytvořit šablonu z virtuálního počítače<br />&#160;&#160;&#160;&#160;přizpůsobení hosta<br />&#160;&#160;&#160;&#160;nasazení šablony<br />&#160;&#160;&#160;&#160;označit jako šablonu<br />&#160;&#160;&#160;&#160;upravit specifikaci přizpůsobení<br />&#160;&#160;&#160;&#160;zvýšit úroveň disků<br />&#160;&#160;&#160;&#160;čtení – specifikace přizpůsobení<br />Konfigurace služeb<br />&#160;&#160;&#160;&#160;povolení oznámení<br />&#160;&#160;&#160;&#160;povolení cyklického dotazování na globální oznamování událostí<br />&#160;&#160;&#160;&#160;spravovat konfiguraci služby<br />&#160;&#160;&#160;&#160;upravit konfiguraci služby<br />Konfigurace služby &#160;&#160;&#160;&#160;dotazů<br />Konfigurace služby &#160;&#160;&#160;&#160;čtení<br />Správa snímků<br />&#160;&#160;&#160;&#160;vytvoření snímku<br />&#160;&#160;&#160;&#160;odebrat snímek<br />&#160;&#160;&#160;&#160;přejmenovat snímek<br />&#160;&#160;&#160;&#160;vrátit snímek<br />vSphere replikace<br />&#160;&#160;&#160;&#160;konfiguraci replikace<br />&#160;&#160;&#160;&#160;spravovat replikaci<br />&#160;&#160;&#160;&#160;monitorování replikace |
| **vService** | Vytvořit závislost<br />Zničit závislost<br />Překonfigurujte konfiguraci závislostí.<br />Aktualizovat závislost |



## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili se základy vSphere řízení přístupu na základě role pro řešení Azure VMware, budete možná chtít získat informace o:

- Podrobnosti o jednotlivých oprávněních v [dokumentaci k produktu VMware](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html).
- [Jak řešení Azure VMware monitoruje a opravuje privátní cloudy](concepts-monitor-repair-private-cloud.md).
- [Jak povolit prostředek řešení Azure VMware](enable-azure-vmware-solution.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VMware product documentation]: https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html


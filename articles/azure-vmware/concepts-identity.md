---
title: Koncepty – identita a přístup
description: Seznamte se s principy identit a přístupu k řešení Azure VMware.
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 7b1880ccdee1d4c3ce25e264c004b1a949134a96
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026900"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Koncepty identity řešení Azure VMware

Privátní cloudy řešení Azure VMware jsou zřízené pomocí vCenter Server a správce NSX-T. Pomocí vCenter můžete spravovat úlohy virtuálních počítačů (VM) a správce NSX-T ke správě a rozšiřování privátního cloudu. Přístup a Správa identit používají pro správce NSX-T roli CloudAdmin pro vCenter a omezená práva správce. 

Další informace najdete v [článku koncepty pro upgrade privátního cloudu][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>přístup k vCenter a identita

V řešení Azure VMware má vCenter integrovaný místní uživatel s názvem cloudadmin a přiřazený k roli CloudAdmin. Místní uživatel cloudadmin se používá k nastavení uživatelů ve službě Active Directory (AD). Obecně platí, že role CloudAdmin vytváří a spravuje úlohy v privátním cloudu. Ale v řešení Azure VMware má role CloudAdmin oprávnění vCenter, která se liší od jiných cloudových řešení VMware.     

- V případě místního nasazení vCenter a ESXi má správce přístup k \@ místnímu účtu správce vCenter vSphere. Můžou mít taky přiřazené víc uživatelů a skupin služby AD. 

- V nasazení řešení Azure VMware nemá správce přístup k uživatelskému účtu správce. Můžou ale k roli CloudAdmin v vCenter přiřadit uživatele a skupiny AD.  

Uživatel privátního cloudu nemá přístup ke službě a nemůže konfigurovat konkrétní součásti pro správu podporované a spravované společností Microsoft. Například clustery, hostitelé, úložiště dat a distribuované virtuální přepínače.

> [!IMPORTANT]
> Řešení Azure VMware nabízí vlastní role na vCenter, ale v současné době je nenabízí na portálu řešení Azure VMware. Další informace najdete v části [Vytvoření vlastních rolí v vCenter](#create-custom-roles-on-vcenter) dále v tomto článku. 

### <a name="view-the-vcenter-privileges"></a>Zobrazit oprávnění vCenter

Můžete zobrazit oprávnění udělená roli CloudAdmin Azure VMware pro řešení Azure VMware v rámci vašeho privátního cloudu vCenter.

1. Přihlaste se ke klientovi SDDC vSphere a přejděte do **nabídky**  >  **Správa**.
1. V části **Access Control** vyberte **role**.
1. V seznamu rolí vyberte **CloudAdmin** a pak vyberte **oprávnění**. 

   :::image type="content" source="media/role-based-access-control-cloudadmin-privileges.png" alt-text="Jak zobrazit oprávnění role CloudAdmin v klientovi vSphere":::

Role CloudAdmin v řešení Azure VMware má následující oprávnění pro vCenter. Další podrobnosti najdete v [dokumentaci k produktu VMware](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html).

| Privilege | Description |
| --------- | ----------- |
| **Upozornění** | Potvrzení upozornění<br />Vytvořit alarm<br />Zakázat akci alarmu<br />Upravit alarm<br />Odebrat alarm<br />Nastavit stav alarmu |
| **Knihovna obsahu** | Přidat položku knihovny<br />Vytvoření odběru publikované knihovny<br />Vytvořit místní knihovnu<br />Vytvořit předplacenou knihovnu<br />Odstranit položku knihovny<br />Odstranit místní knihovnu<br />Odstranit předplacenou knihovnu<br />Odstranění předplatného publikované knihovny<br />Stažení souborů<br />Vyřazení položek knihovny<br />Vyřazení odebírané knihovny<br />Import úložiště<br />Informace o předplatném testu<br />Publikování položky knihovny pro její předplatitele<br />Publikování knihovny pro své předplatitele<br />Čtení úložiště<br />Synchronizovat položku knihovny<br />Synchronizovat předplacenou knihovnu<br />Typ introspekce<br />Aktualizovat nastavení konfigurace<br />Soubory aktualizací<br />Aktualizovat knihovnu<br />Aktualizovat položku knihovny<br />Aktualizovat místní knihovnu<br />Aktualizace odebírané knihovny<br />Aktualizace předplatného publikované knihovny<br />Zobrazit nastavení konfigurace |
| **Kryptografické operace** | Přímý přístup |
| **Úložiště dat** | Přidělit místo<br />Procházet úložiště dat<br />Konfigurace úložiště dat<br />Operace se soubory na nízké úrovni<br />Odebrat soubory<br />Aktualizace metadat virtuálního počítače |
| **Složka** | Vytvořit složku<br />Odstranit složku<br />Přesunout složku<br />Přejmenovat složku |
| **Globální** | Zrušit úlohu<br />Globální značka<br />Zdravotnictví<br />Událost protokolu<br />Správa vlastních atributů<br />Manažeři služeb<br />Nastavit vlastní atribut<br />Systémová značka |
| **Hostitel** | vSphere replikace<br />&#160;&#160;&#160;&#160;spravovat replikaci |
| **Síť** | Přiřadit síť |
| **Oprávnění** | Upravit oprávnění<br />Upravit roli |
| **Profil** | Zobrazení úložiště řízené profilem |
| **Prostředek** | Použít doporučení<br />Přiřazení vApp ke fondu zdrojů<br />Přiřadit virtuální počítač k fondu zdrojů<br />Vytvořit fond zdrojů<br />Migrace vypnutého virtuálního počítače<br />Migrace zapnutá na virtuálním počítači<br />Upravit fond zdrojů<br />Přesunout fond zdrojů<br />VMotion dotazu<br />Odebrat fond zdrojů<br />Přejmenovat fond zdrojů |
| **Naplánovaná úloha** | Vytvořit úlohu<br />Upravit úlohu<br />Odebrat úlohu<br />Spustit úlohu |
| **Relace** | Zpráva<br />Ověřit relaci |
| **Zobrazení úložiště** | Zobrazení |
| **vApp** | Přidat virtuální počítač<br />Přiřadit fond zdrojů<br />Přiřadit vApp<br />Klonování<br />Vytvořit<br />Odstranit<br />Export<br />Import<br />Přesunout<br />Napájení vypnuto<br />Zapnout<br />přejmenování<br />Suspend<br />Zrušit registraci<br />Zobrazit prostředí OVF<br />Konfigurace aplikace vApp<br />konfigurace instance vApp<br />Konfigurace vApp managedBy<br />Konfigurace prostředků vApp |
| **Virtuální počítač** | Změnit konfiguraci<br />&#160;&#160;&#160;&#160;získání zapůjčení disku<br />&#160;&#160;&#160;&#160;přidat existující disk<br />&#160;&#160;&#160;&#160;přidat nový disk<br />&#160;&#160;&#160;&#160;přidat nebo odebrat zařízení<br />&#160;&#160;&#160;&#160;Pokročilá konfigurace<br />&#160;&#160;&#160;&#160;Změna počtu PROCESORů<br />&#160;&#160;&#160;&#160;změnit paměť<br />&#160;&#160;&#160;&#160;změnit nastavení<br />&#160;&#160;&#160;&#160;změnit umístění swapfile<br />Prostředek změny &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;Konfigurace hostitelského zařízení USB<br />&#160;&#160;&#160;&#160;konfigurace nezpracovaného zařízení<br />&#160;&#160;&#160;&#160;konfigurace managedBy<br />&#160;&#160;&#160;&#160;zobrazení nastavení připojení<br />&#160;&#160;&#160;&#160;– zvětšit virtuální disk<br />&#160;&#160;&#160;&#160;upravit nastavení zařízení<br />Kompatibilita &#160;&#160;&#160;&#160;dotazů na odolnost proti chybám<br />&#160;&#160;&#160;&#160;nevlastněných souborů dotazů<br />Opětovné načtení &#160;&#160;&#160;&#160;z cest<br />&#160;&#160;&#160;&#160;odebrat disk<br />Přejmenování &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;resetovat informace o hostovi<br />&#160;&#160;&#160;&#160;nastavit poznámku<br />&#160;&#160;&#160;&#160;přepínání sledování změn disků<br />Přepínací tlačítko pro rozvětvení &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;upgrade kompatibility virtuálních počítačů<br />Upravit inventář<br />&#160;&#160;&#160;&#160;vytvořit z existujícího<br />&#160;&#160;&#160;&#160;vytvořit nový<br />&#160;&#160;&#160;&#160;přesun<br />&#160;&#160;&#160;&#160;registraci<br />&#160;&#160;&#160;&#160;odebrat<br />&#160;&#160;&#160;&#160;zrušit registraci<br />Operace hosta<br />&#160;&#160;&#160;&#160;úpravy aliasu operace hosta<br />&#160;&#160;&#160;&#160;dotaz na alias operace hosta<br />&#160;&#160;&#160;&#160;úpravy operací hosta<br />&#160;&#160;&#160;&#160;provádění programu operace hosta<br />&#160;&#160;&#160;&#160;dotazy na operace hosta<br />Interakce<br />Odpověď na &#160;&#160;&#160;&#160;otázku<br />&#160;&#160;&#160;&#160;operaci zálohování na virtuálním počítači<br />&#160;&#160;&#160;&#160;nakonfigurovat médium CD<br />&#160;&#160;&#160;&#160;konfigurace disketových médií<br />&#160;&#160;&#160;&#160;připojit zařízení<br />Interakce konzoly &#160;&#160;&#160;&#160;<br />Vytvoření snímku obrazovky &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;defragmentovat všechny disky<br />&#160;&#160;&#160;&#160;přetahování<br />&#160;&#160;&#160;&#160;správu hostovaného operačního systému pomocí rozhraní API pro VIX<br />&#160;&#160;&#160;&#160;vložení kódů kontroly USB HID<br />&#160;&#160;&#160;&#160;instalaci nástrojů VMware<br />Pozastavení nebo zastavení &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;operace vymazání nebo zmenšení<br />Vypínání &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;zapnutí<br />&#160;&#160;&#160;&#160;relace záznamu na virtuálním počítači<br />&#160;&#160;&#160;&#160;relace přehrání na virtuálním počítači<br />Pozastavení &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;pozastavit odolnost proti chybám<br />&#160;&#160;&#160;&#160;testovací převzetí služeb při selhání<br />&#160;&#160;&#160;&#160;sekundární virtuální počítač pro restartování testu<br />&#160;&#160;&#160;&#160;vypnout odolnost proti chybám<br />&#160;&#160;&#160;&#160;zapnout odolnost proti chybám<br />Zřizování<br />&#160;&#160;&#160;&#160;povolení přístupu k disku<br />&#160;&#160;&#160;&#160;povolení přístupu k souborům<br />&#160;&#160;&#160;&#160;povolí přístup k disku jen pro čtení.<br />&#160;&#160;&#160;&#160;povolení stahování virtuálního počítače<br />&#160;&#160;&#160;&#160;šablonu klonování<br />&#160;&#160;&#160;&#160;klonovat virtuální počítač<br />&#160;&#160;&#160;&#160;vytvořit šablonu z virtuálního počítače<br />&#160;&#160;&#160;&#160;přizpůsobení hosta<br />&#160;&#160;&#160;&#160;nasazení šablony<br />&#160;&#160;&#160;&#160;označit jako šablonu<br />&#160;&#160;&#160;&#160;upravit specifikaci přizpůsobení<br />&#160;&#160;&#160;&#160;zvýšit úroveň disků<br />&#160;&#160;&#160;&#160;čtení – specifikace přizpůsobení<br />Konfigurace služeb<br />&#160;&#160;&#160;&#160;povolení oznámení<br />&#160;&#160;&#160;&#160;povolení cyklického dotazování na globální oznamování událostí<br />&#160;&#160;&#160;&#160;spravovat konfiguraci služby<br />&#160;&#160;&#160;&#160;upravit konfiguraci služby<br />Konfigurace služby &#160;&#160;&#160;&#160;dotazů<br />Konfigurace služby &#160;&#160;&#160;&#160;čtení<br />Správa snímků<br />&#160;&#160;&#160;&#160;vytvoření snímku<br />&#160;&#160;&#160;&#160;odebrat snímek<br />&#160;&#160;&#160;&#160;přejmenovat snímek<br />&#160;&#160;&#160;&#160;vrátit snímek<br />vSphere replikace<br />&#160;&#160;&#160;&#160;konfiguraci replikace<br />&#160;&#160;&#160;&#160;spravovat replikaci<br />&#160;&#160;&#160;&#160;monitorování replikace |
| **vService** | Vytvořit závislost<br />Zničit závislost<br />Překonfigurujte konfiguraci závislostí.<br />Aktualizovat závislost |
| **označování vSphere** | Přiřadit a zrušit přiřazení značky vSphere<br />Vytvořit značku vSphere<br />Vytvořit kategorii značek vSphere<br />Odstranit značku vSphere<br />Odstranit kategorii značek vSphere<br />Upravit značku vSphere<br />Upravit kategorii značek vSphere<br />Upravit pole UsedBy pro kategorii<br />Upravit pole UsedBy pro značku |

### <a name="create-custom-roles-on-vcenter"></a>Vytváření vlastních rolí na vCenter

Řešení Azure VMware podporuje použití vlastních rolí se stejnými nebo méně oprávněními než role CloudAdmin. 

Role CloudAdmin může vytvářet, upravovat nebo odstraňovat vlastní role, které mají oprávnění menší nebo rovna jejich aktuální roli. Možná budete moct vytvořit role, které mají oprávnění větší než CloudAdmin, ale nebudete je moct přiřadit k žádným uživatelům nebo skupinám ani tuto roli odstranit.

Aby se zabránilo vytváření rolí, které se nedají přiřadit ani odstranit, řešení Azure VMware doporučuje klonování role CloudAdmin jako základu pro vytváření nových vlastních rolí.

#### <a name="create-a-custom-role"></a>Vytvoření vlastní role
1. Přihlaste se k vCenter pomocí cloudadmin \@ vSphere. Local nebo uživatele s rolí cloudadmin.
2. Přejděte do části konfigurace **rolí** a vyberte **nabídky**  >  **Správa**  >  **Access Control**  >  **rolí**.
3. Vyberte roli **CloudAdmin** a vyberte ikonu **Akce klonovat roli** .

   > [!NOTE] 
   > Neklonujte roli **správce** . Tuto roli nelze použít a vytvořené vlastní role nelze odstranit pomocí cloudadmin \@ vSphere. Local.

4. Zadejte název, který chcete klonovat roli.
5. Přidejte nebo odeberte oprávnění pro roli a vyberte **OK**. Naklonovaná role by se teď měla zobrazit v seznamu **rolí** .


#### <a name="use-a-custom-role"></a>Použití vlastní role

1. Přejděte k objektu, který vyžaduje přidané oprávnění. Pokud chcete například použít oprávnění pro složku, přejděte na   >  **virtuální počítače nabídky a**  >  **název složky** šablony.
1. Klikněte pravým tlačítkem na objekt a vyberte **Přidat oprávnění**.
1. V okně **Přidat oprávnění** vyberte v rozevíracím seznamu **uživatel** zdroj identity, kde se dá najít skupina nebo uživatel.
1. Po výběru zdroje identity v části **uživatel** vyhledejte uživatele nebo skupinu. 
1. Vyberte roli, která bude použita pro uživatele nebo skupinu.
1. V případě potřeby zaškrtněte možnost **rozšířit na podřízené objekty** a vyberte **OK**.
   Přidaná oprávnění se zobrazí v sekci **oprávnění** pro daný objekt.

## <a name="nsx-t-manager-access-and-identity"></a>Přístup a identita správce NSX – T

Pro přístup ke Správci NSX-T použijte účet *správce* . Má úplná oprávnění a umožňuje vytvářet a spravovat brány (T1) vrstvy 1 (T1), segmenty (logické přepínače) a všechny služby. Oprávnění poskytují přístup k bráně NSX-T úrovně 0 (T0). Změna v bráně T0 by mohla způsobit snížení výkonu sítě nebo přístupu k privátnímu cloudu. Otevřete žádost o podporu v Azure Portal, abyste požádali o změny v bráně NSX-T T0.

 
## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s koncepty přístupu k řešení a identitou Azure VMware, se můžete seznámit s těmito informacemi:

- [Koncepce upgradu privátního cloudu](concepts-upgrades.md)
- [Jak povolit prostředek řešení Azure VMware](enable-azure-vmware-solution.md)
- [Podrobnosti o každém oprávnění](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html)
- [Jak řešení Azure VMware monitoruje a opravuje privátní cloudy](concepts-monitor-repair-private-cloud.md)
- [Jak povolit prostředek řešení Azure VMware](enable-azure-vmware-solution.md)


<!-- LINKS - external-->
[VMware product documentation]: https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md

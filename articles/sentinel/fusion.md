---
title: Rozšířená detekce útoků s více fázemi v Azure Sentinel
description: Pomocí technologie Fusion v Azure Sentinel můžete snížit únavu výstrah a vytvořit incidenty, které jsou založené na pokročilé detekci útoku na více fází.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: ba872f221f3bde29f0bb48b04dc2259d3ab4938a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906272"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Rozšířená detekce útoků s více fázemi v Azure Sentinel


> [!IMPORTANT]
> Některé funkce Fusion v Azure Sentinel jsou momentálně ve **verzi Public Preview**.
> Tyto funkce se poskytují bez smlouvy o úrovni služeb a nedoporučují se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pomocí technologie Fusion založené na strojovém učení může Azure Sentinel automaticky detekovat útoky s více fázemi tím, že identifikuje kombinace chování neobvyklé a podezřelých aktivit, které jsou pozorovány v různých fázích dezaktivačního řetězu. Na základě těchto zjištění Azure Sentinel generuje incidenty, které by jinak bylo obtížné zachytit. Tyto incidenty sestávají ze dvou nebo více výstrah nebo aktivit. V takovém případě mají tyto incidenty nízkou hlasitost, vysokou přesnost a vysokou závažnost.

Tato technologie detekce přizpůsobená vašemu prostředí neomezuje jenom falešně pozitivní sazby, ale může také detekovat útoky s omezenými nebo chybějícími informacemi.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Konfigurace pro pokročilou detekci útoků s více fázemi

Tato detekce je ve výchozím nastavení povolená v Azure Sentinel. Pokud chcete stav ověřit nebo ho zakázat v případě, že používáte alternativní řešení k vytváření incidentů na základě více výstrah, postupujte podle následujících pokynů:

1. Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com).

1. Přejít na **Azure Sentinel**  >  **Configuration**  >  **Analytics**

1. Vyberte **aktivní pravidla**a potom pomocí filtrování seznamu pro typ pravidla **fúze** Najděte ve sloupci **název** **pokročilou detekci útoku s více fázemi** . Zkontrolujte sloupec **stav** a potvrďte, jestli je toto zjišťování povolené nebo zakázané.

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="{ALT-text}":::

1. Chcete-li změnit stav, vyberte tuto položku a v okně **Pokročilé zjišťování útoků s více fázemi** vyberte možnost **Upravit**.

1. V okně **Průvodce vytvořením pravidla** se automaticky vybere Změna stavu, takže vyberte **Další: zkontrolovat**a pak **Uložit**. 

 Vzhledem k tomu, že typ pravidla **fúze** obsahuje pouze jedno pravidlo, které nelze upravit, šablony pravidel nelze použít pro tento typ pravidla.

> [!NOTE]
> Služba Azure Sentinel aktuálně používá ke studiu systémů strojového učení 30 dní historických dat. Tato data se vždycky šifrují pomocí klíčů Microsoftu při jejich předávání prostřednictvím kanálu strojového učení. Školicí data se ale nešifrují pomocí [zákaznických klíčů (CMK)](customer-managed-keys.md) , pokud jste v pracovním prostoru Sentinel Azure povolili CMK. Pokud se chcete odhlásit z fúze, přejděte na **Azure Sentinel**   \>  **Configuration**   \>  **Analytics \> aktivní pravidla \> Upřesnit detekci útoků ve více fázích** a ve sloupci **stav** vyberte **zakázat.**

## <a name="attack-detection-scenarios"></a>Scénáře detekce útoků

V následující části jsou uvedené typy scénářů korelace seskupených podle klasifikace hrozeb, kterou služba Azure Sentinel vyhledává pomocí technologie Fusion.

Jak je uvedeno výše, vzhledem k tomu, že fúze koreluje více výstrah zabezpečení z různých produktů za účelem zjištění pokročilých útoků na více verzí, **se na stránce** **incidenty** Sentinel Azure zobrazují úspěšné detekce Fusion a ne jako **výstrahy** v tabulce **výstrahy zabezpečení** v **protokolech**.

Aby bylo možné tyto scénáře detekce útoků využívajících technologii Fusion povolit, musí být všechny uvedené zdroje dat ingestované pomocí přidružených datových konektorů Azure Sentinel.

> [!NOTE]
> Některé z těchto scénářů jsou ve **verzi Public Preview**. Budou tak označeny.

## <a name="compute-resource-abuse"></a>Zneužití prostředků COMPUTE

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>Několik aktivit vytváření virtuálních počítačů po podezřelých Azure Active Directory přihlášení
Tento scénář je aktuálně ve **verzi Public Preview**.

**Mitre ATT&CK taktiku:** Počáteční přístup, dopad 

**Mitre technologie ATT&CK:** Platný účet (T1078), zneužití prostředků (T1496)

**Zdroje datových konektorů:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Popis:** Incidenty fúze tohoto typu označují, že neobvyklé počet virtuálních počítačů, které se v jedné relaci vytvořily po podezřelém přihlášení k účtu Azure AD. Tento typ výstrahy oznamuje vysokou mírou spolehlivosti, že účet uvedený v popisu incidentu fúze byl zneužit a používá se k vytvoření nových virtuálních počítačů pro neoprávněné účely, jako je třeba spuštění operací kryptografického dolování. Upozornění na podezřelé výstrahy týkající se přihlášení Azure AD s výstrahou aktivity vytvoření více virtuálních počítačů jsou:

- **Nepovedlo se cestovat do neobvyklých míst, což vede k vytvoření více virtuálních počítačů.**

- **Přihlašovací událost z neznámého umístění, což vede k několika aktivitám vytváření virtuálních počítačů**

- **Přihlašovací událost z nakaženého zařízení, což vede k několika činnostem při vytváření virtuálních počítačů**

- **Událost přihlášení z anonymní IP adresy, která vede k vytvoření více virtuálních počítačů**

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji, které vedly k několika aktivitám vytváření virtuálních počítačů**

## <a name="data-exfiltration"></a>Exfiltrace dat

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>Poštovní schránka Office 365 po podezřelém přihlášení ke službě Azure AD exfiltrace

**Mitre ATT&CK taktiku:** Počáteční přístup, exfiltrace, kolekce

**Mitre technologie ATT&CK:** Platný účet (T1078), kolekce e-mailů (T1114), automatizované exfiltrace (T1020)

**Zdroje datových konektorů:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Popis:** Incidenty fúze tohoto typu označují, že v doručené poště uživatele bylo po podezřelém přihlášení k účtu služby Azure AD nastaveno pravidlo pro přesměrování doručené pošty. Tato indikace poskytuje vysokou jistotu, že účet uživatele (uvedený v popisu incidentu fúze) je napadený a že se použil k exfiltrovatí dat ze sítě vaší organizace povolením pravidla předávání poštovní schránky bez vědomí uživatele skutečný uživatel. Upozornění na podezřelé výstrahy služby Azure AD s upozorněním na exfiltrace poštovní schránky pro Office 365 jsou:

- **Nemožná cesta do neobvyklých míst, které vede k exfiltrace poštovní schránky Office 365**

- **Přihlašovací událost z neznámého umístění, které vede k exfiltrace poštovní schránky Office 365**

- **Přihlašovací událost z nakaženého zařízení, které vede k exfiltrace poštovní schránky Office 365**

- **Událost přihlášení z anonymní IP adresy, která vede k exfiltrace poštovní schránky Office 365**

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k poštovní schránce Office 365 exfiltrace**

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>Hromadně stahovat soubory po podezřelé přihlášení ke službě Azure AD

**Mitre ATT&CK taktiku:** Počáteční přístup, exfiltrace

**Mitre technologie ATT&CK:** Platný účet (T1078)

**Zdroje datových konektorů:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Popis:** Incidenty fúze tohoto typu označují, že neobvyklé počet souborů stažených uživatelem po podezřelém přihlášení k účtu služby Azure AD. Tato indikace poskytuje vysokou jistotu, že účet uvedený v popisu incidentu fúze byl zneužit a byl použit k exfiltrovatí dat ze sítě vaší organizace. Upozornění na podezřelé výstrahy služby Azure AD s upozorněním na stažení hromadného souboru jsou:  

- **Nemožná cesta do neobvyklých míst, které vede ke stažení souboru**

- **Událost přihlášení z neznámého umístění, které vede k hromadnému stažení souboru**

- **Událost přihlášení z nakaženého zařízení, které vede k hromadnému stažení souboru**

- **Událost přihlášení z anonymní IP adresy, která umožňuje stažení souboru**

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucím ke stažení souboru**

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>Hromadné sdílení souborů po podezřelém přihlášení ke službě Azure AD

**Mitre ATT&CK taktiku:** Počáteční přístup, exfiltrace

**Mitre technologie ATT&CK:** Platný účet (T1078), služba exfiltrace over Web Service (T1567)

**Zdroje datových konektorů:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Popis:** Incidenty fúze tohoto typu označují, že několik souborů nad určitou prahovou hodnotou bylo sdíleno ostatním po podezřelém přihlášení k účtu služby Azure AD. Tato indikace poskytuje vysokou jistotu, že účet uvedený v popisu incidentu fúze byl zneužit a slouží k exfiltrovatí dat ze sítě vaší organizace sdílením souborů, jako jsou dokumenty, tabulky atd., s neoprávněnými uživateli ke škodlivým účelům. V upozorněních, které jsou podezřelé při přihlašování pomocí hromadného sdílení souborů, se zobrazí následující:  

- **Nemožná cesta do neobvyklých míst, které vede k hromadnému sdílení souborů**

- **Událost přihlášení z neznámého umístění, které vede k hromadnému sdílení souborů**

- **Událost přihlášení z nakaženého zařízení, které vede k hromadnému sdílení souborů**

- **Událost přihlášení z anonymní IP adresy, která vede ke sdílení souborů**

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k hromadnému sdílení souborů**

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Podezřelá pravidla pro manipulaci s doručenou poštou nastavená po podezřelém přihlášení k Azure AD
Tento scénář patří do dvou klasifikací hrozeb v tomto seznamu: **exfiltrace dat** a **boční pohyb**. V zájmu srozumitelnosti se tato položka zobrazuje v obou částech.

Tento scénář je aktuálně ve **verzi Public Preview**.

**Mitre ATT&CK taktiku:** Počáteční přístup, boční pohyb, exfiltrace

**Mitre technologie ATT&CK:** Platný účet (T1078), interní spear phishing (T1534)

**Zdroje datových konektorů:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Popis:** Incidenty fúze tohoto typu označují, že pravidla doručených zpráv neobvyklé byla nastavena v doručené poště uživatele po podezřelém přihlášení k účtu služby Azure AD. Tím je zajištěno, že účet uvedený v popisu incidentu fúze byl zneužit a byl použit k manipulaci s pravidly e-mailové schránky uživatele pro škodlivé účely. Důvodem může být to, že by útočník mohl exfiltrovat data ze sítě organizace. Případně se může útočník pokusit vygenerovat e-maily v organizaci podvodných zpráv v rámci organizace (obejít mechanismy detekce útoků phishing cílené na e-mail z externích zdrojů) pro účely pozdějšího přesunu získáním přístupu k dalším uživatelským a/nebo privilegovaným účtům. Výstrahy podezřelých přihlašovacích upozornění služby Azure AD s upozorněním na pravidla manipulace s podezřelými doručenými oznámeními jsou:  

- **Nepovedlo se cestovat do neobvyklých míst, které vede k podezřelému pravidlu manipulace**

- **Přihlašovací událost z neznámého umístění vedoucí na podezřelé pravidlo manipulace Doručená pošta**

- **Přihlašovací událost z nakaženého zařízení, které vede k podezřelému pravidlu manipulace s doručenou poštou**

- **Událost přihlášení z anonymní IP adresy, která vede na podezřelé pravidlo manipulace s doručenou poštou**

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k podezřelému pravidlu manipulace s doručenou poštou**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>Různé aktivity sdílení sestav Power BI po podezřelém přihlášení ke službě Azure AD 
Tento scénář je aktuálně ve **verzi Public Preview**.

**Mitre ATT&CK taktiku:** Počáteční přístup, exfiltrace 

**Mitre technologie ATT&CK:** Platný účet (T1078), služba exfiltrace over Web Service (T1567)

**Zdroje datových konektorů:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Popis:** Incidenty fúze tohoto typu označují, že neobvyklé počet sestav Power BI v jedné relaci se sdílí po podezřelém přihlášení k účtu Azure AD. Tato indikace poskytuje vysokou jistotu, že účet uvedený v popisu incidentu fúze byl zneužit a byl použit k exfiltrovatí dat ze sítě vaší organizace sdílením Power BI sestav s neoprávněnými uživateli ke škodlivým účelům. Permutace podezřelých výstrah pro přihlášení Azure AD s více aktivitami sdílení sestav Power BI:  

- **Nemožná cesta do neobvyklých umístění, která by vedla k více aktivitám sdílení sestav Power BI.**

- **Přihlašovací událost z neznámého umístění, což vede k více aktivitám sdílení sestav Power BI.**

- **Událost přihlášení z nakaženého zařízení, které má za následek více Power BI aktivit sdílení sestav**

- **Událost přihlášení z anonymní IP adresy, která představí více Power BI aktivit sdílení sestav**

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k více aktivitám sdílení sestav Power BI**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>Podezřelá Power BI sdílení sestav po podezřelém přihlášení ke službě Azure AD
Tento scénář je aktuálně ve **verzi Public Preview**.

**Mitre ATT&CK taktiku:** Počáteční přístup, exfiltrace 

**Mitre technologie ATT&CK:** Platný účet (T1078), služba exfiltrace over Web Service (T1567)

**Zdroje datových konektorů:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Popis:** Incidenty fúze tohoto typu označují, že došlo k podezřelé aktivitě sdílení sestav Power BI po podezřelém přihlášení k účtu služby Azure AD. Aktivita sdílení byla identifikována jako podezřelá, protože sestava Power BI obsahovala citlivé informace identifikované pomocí zpracování přirozeného jazyka a protože byla sdílena s externí e-mailovou adresou, publikována na webu nebo jako snímek na externě odebírané e-mailovou adresu. Tato výstraha indikuje vysokou jistotu, že účet uvedený v popisu incidentu fúze byl zneužit a byl použit k exfiltrovat citlivých dat z vaší organizace tím, že sdílí Power BI sestavy s neoprávněnými uživateli ke škodlivým účelům. K podezřelým Power BIm sdílení sestav se používají permutace podezřelých upozornění na přihlášení ke službě Azure AD:  

- **Nemožná cesta do neobvyklých míst, což vede k podezřelým Power BI sdílení sestav**

- **Přihlašovací událost z neznámého umístění, což vede k podezřelým Power BI sdílení sestav**

- **Událost přihlášení z nakaženého zařízení, které vedlo k podezřelým Power BI sdílení sestav**

- **Událost přihlášení z anonymní IP adresy, která vede k podezřelým Power BI sdílení sestav**

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k podezřelému Power BI sdílení sestav**

## <a name="data-destruction"></a>Zničení dat

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>Hromadné odstranění souborů po podezřelém přihlášení ke službě Azure AD

**Mitre ATT&CK taktiku:** Počáteční přístup, dopad

**Mitre technologie ATT&CK:** Platný účet (T1078), zničení dat (T1485)

**Zdroje datových konektorů:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Popis:** Incidenty fúze tohoto typu označují, že neobvyklé počet jedinečných souborů, který se odstranil po podezřelém přihlášení k účtu Azure AD. Tím se zobrazí informace o tom, že účet uvedený v popisu incidentu fúze mohl být napadený a byl použit k zničení dat pro škodlivé účely. Upozornění na podezřelé výstrahy týkající se přihlášení Azure AD s hromadnou odstraňováním souborů:  

- **Nemožná cesta do neobvyklých míst, které by vedlo k odstranění souboru**

- **Událost přihlášení z neznámého umístění, které vede k odstranění souboru**

- **Událost přihlášení z nakaženého zařízení, které by vedlo k odstranění souboru**

- **Událost přihlášení z anonymní IP adresy, která vede k odstranění souboru**

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k hromadnému odstranění souboru**

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>Podezřelá aktivita odstranění e-mailu po podezřelém přihlášení ke službě Azure AD
Tento scénář je aktuálně ve **verzi Public Preview**.

**Mitre ATT&CK taktiku:** Počáteční přístup, dopad 

**Mitre technologie ATT&CK:** Platný účet (T1078), zničení dat (T1485)

**Zdroje datových konektorů:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Popis:** Incidenty fúze tohoto typu označují, že v jedné relaci se odstranil neobvyklé počet e-mailů po podezřelém přihlášení k účtu Azure AD. To znamená, že účet uvedený v popisu incidentu fúze mohl být napadený a byl použit k zničení dat škodlivých účelů, jako je například poškození organizace nebo skrytí e-mailové aktivity týkající se spamu. Upozornění na podezřelé výstrahy týkající se přihlášení k Azure AD s podezřelou aktivitou aktivity odstranění e-mailu jsou tyto:   

- **Nemožná cesta do neobvyklých míst, které vede k podezřelé aktivitě e-mail**

- **Událost přihlášení z neznámého umístění, které vede k podezřelé aktivitě e-mailového odstranění**

- **Přihlašovací událost z nakaženého zařízení, která vede k podezřelé aktivitě e-mailového odstranění**

- **Událost přihlášení z anonymní IP adresy, která vede k podezřelé aktivitě e-mailového odstranění**

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji, která vede k podezřelé aktivitě e-mailového odstranění**

## <a name="denial-of-service"></a>Odepření služby

### <a name="multiple-vm-delete-activities-following-suspicious-azure-ad-sign-in"></a>Několik aktivit odstranění virtuálních počítačů po podezřelém přihlášení ke službě Azure AD
Tento scénář je aktuálně ve **verzi Public Preview**.

**Mitre ATT&CK taktiku:** Počáteční přístup, dopad

**Mitre technologie ATT&CK:** Platný účet (T1078), koncový bod DOS služby (T1499)

**Zdroje datových konektorů:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Popis:** Incidenty fúze tohoto typu označují, že neobvyklé počet virtuálních počítačů, které se v jedné relaci odstranily po podezřelém přihlášení k účtu Azure AD. Tato indikace poskytuje vysokou jistotu, že účet uvedený v popisu incidentu fúze byl zneužit a byl použit k pokusu o narušení nebo zničení cloudového prostředí organizace. Upozornění na podezřelé výstrahy týkající se přihlášení ke službě Azure AD pomocí výstrahy s více virtuálními počítači pro odstranění aktivit jsou:  

- **Nepovedlo se cestovat do neobvyklých míst, které by vedlo k několika aktivitám odstranění**

- **Přihlašovací událost z neznámého umístění, což vede k několika aktivitám odstranění virtuálního počítače**

- **Událost přihlášení z nakaženého zařízení, které vedlo k několika aktivitám odstranění virtuálního počítače**

- **Událost přihlášení z anonymní IP adresy, která vede k několika aktivitám odstranění virtuálního počítače**

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k více aktivitám odstranění virtuálního počítače**

## <a name="lateral-movement"></a>Laterální pohyb

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>Zosobnění Office 365 po podezřelém přihlášení ke službě Azure AD

**Mitre ATT&CK taktiku:** Počáteční přístup, boční pohyb

**Mitre technologie ATT&CK:** Platný účet (T1078), interní spear phishing (T1534)

**Zdroje datových konektorů:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Popis:** Incidenty fúze tohoto typu označují, že neobvyklé počet zosobněných akcí při podezřelém přihlašování z účtu Azure AD nastal. V některém softwaru existují možnosti, jak uživatelům dovolit zosobnit jiné uživatele. Například e-mailové služby umožňují uživatelům autorizovat ostatním uživatelům odesílat e-maily jménem. Tato výstraha indikuje větší jistotu, že účet uvedený v popisu incidentu fúze byl napadený a byl použit k provádění aktivit zosobnění pro škodlivé účely, jako je například odesílání podvodných e-mailů pro distribuci malwaru nebo při jejich přesunu. K upozorněním na podezřelé výstrahy služby Azure AD s výstrahou zosobnění systému Office 365 patří:  

- **Nemožná cesta do neobvyklých míst, které vede k zosobnění Office 365**

- **Přihlašovací událost z neznámého umístění, které vede k zosobnění Office 365**

- **Přihlašovací událost z nakaženého zařízení, které vede k zosobnění Office 365**

- **Událost přihlášení z anonymní IP adresy, která vede k zosobnění systému Office 365**

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k zosobnění systému Office 365**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Podezřelá pravidla pro manipulaci s doručenou poštou nastavená po podezřelém přihlášení k Azure AD
Tento scénář patří do dvou klasifikací hrozeb v tomto seznamu: **boční pohyb** a **exfiltrace dat**. V zájmu srozumitelnosti se tato položka zobrazuje v obou částech.

Tento scénář je aktuálně ve **verzi Public Preview**.

**Mitre ATT&CK taktiku:** Počáteční přístup, boční pohyb, exfiltrace

**Mitre technologie ATT&CK:** Platný účet (T1078), interní spear phishing (T1534), automatizované exfiltrace (T1020)

**Zdroje datových konektorů:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Popis:** Incidenty fúze tohoto typu označují, že pravidla doručených zpráv neobvyklé byla nastavena v doručené poště uživatele po podezřelém přihlášení k účtu služby Azure AD. Tato indikace poskytuje vysokou jistotu, že účet uvedený v popisu incidentu fúze byl zneužit a byl použit k manipulaci s pravidly e-mailové schránky uživatele pro škodlivé účely. Důvodem může být to, že by útočník mohl exfiltrovat data ze sítě organizace. Případně se může útočník pokusit vygenerovat e-maily v organizaci podvodných zpráv v rámci organizace (obejít mechanismy detekce útoků phishing cílené na e-mail z externích zdrojů) pro účely pozdějšího přesunu získáním přístupu k dalším uživatelským a/nebo privilegovaným účtům. Výstrahy podezřelých přihlašovacích upozornění služby Azure AD s upozorněním na pravidla manipulace s podezřelými doručenými oznámeními jsou:

- **Nepovedlo se cestovat do neobvyklých míst, které vede k podezřelému pravidlu manipulace**

- **Přihlašovací událost z neznámého umístění vedoucí na podezřelé pravidlo manipulace Doručená pošta**

- **Přihlašovací událost z nakaženého zařízení, které vede k podezřelému pravidlu manipulace s doručenou poštou**

- **Událost přihlášení z anonymní IP adresy, která vede na podezřelé pravidlo manipulace s doručenou poštou**

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k podezřelému pravidlu manipulace s doručenou poštou**

## <a name="malicious-administrative-activity"></a>Škodlivá aktivita správy

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>Podezřelá aktivita správy cloudových aplikací po podezřelém přihlášení ke službě Azure AD

**Mitre ATT&CK taktiku:** Počáteční přístup, trvalost, ochrana proti úniku dat, boční oběh, shromažďování, exfiltrace a dopad

**Mitre technologie ATT&CK:** NENÍ K DISPOZICI

**Zdroje datových konektorů:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Popis:** Incidenty fúze tohoto typu označují, že v jedné relaci se provedl neobvyklé počet aktivit správy, a to po podezřelém přihlášení ke službě Azure AD ze stejného účtu. To znamená, že účet uvedený v popisu incidentu fúze mohl být ohrožen a byl použit k provedení libovolného počtu neautorizovaných akcí správy se škodlivým záměrem. To také znamená, že došlo k ohrožení zabezpečení účtu s oprávněními správce. Upozornění na podezřelé výstrahy týkající se přihlášení Azure AD s podezřelou aktivitou správy cloudové aplikace jsou tyto:  

- **Nepovedlo se cestovat do neobvyklých míst, které by vedlo k podezřelé aktivitě správy Cloud**

- **Přihlašovací událost z neznámého umístění vedoucí k podezřelé aktivitě správy cloudové aplikace**

- **Přihlašovací událost z nakaženého zařízení vedoucí k podezřelé aktivitě správy cloudové aplikace**

- **Událost přihlášení z anonymní IP adresy, která vede k podezřelé aktivitě správy cloudové aplikace**

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k podezřelé aktivitě správy cloudové aplikace**

## <a name="malicious-execution-with-legitimate-process"></a>Zlomyslné spouštění s legitimním procesem

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Prostředí PowerShell provedlo podezřelé síťové připojení, za kterým následuje neobvyklé provoz označený bránou firewall pro Palo Alto Networks.
Tento scénář je aktuálně ve **verzi Public Preview**.

**Mitre ATT&CK taktiku:** Realizaci

**Mitre technologie ATT&CK:** Interpret příkazů a skriptování (T1059)

**Zdroje datových konektorů:** Microsoft Defender pro koncový bod (dříve Microsoft Defender Advanced Threat Protection nebo MDATP), Palo Alto Networks 

**Popis:** Incidenty fúze tohoto typu označují, že se odeslal požadavek na odchozí připojení prostřednictvím příkazu PowerShellu. za tímto se neobvyklé příchozí aktivity, které brána firewall Palo Alto sítě zjistila. To znamená, že útočník mohl získat přístup k vaší síti a snaží se provést škodlivé akce. Pokusy o připojení pomocí PowerShellu, které následují tento model, můžou být označením aktivity malwaru a řízení, požadavky na stažení dalšího malwaru nebo útočníka vytvářející vzdálený interaktivní přístup. Stejně jako u všech "živých útoků" se může jednat o legitimní použití PowerShellu. Spuštění příkazu PowerShellu následovaný podezřelou příchozí bránou firewall ale zvyšuje jistotu, že je prostředí PowerShell používáno škodlivým způsobem a mělo by se ještě prozkoumat. V protokolech Palo Alto se Azure Sentinel zaměřuje na [protokoly hrozeb](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)a provoz se považuje za podezřelý, pokud jsou povolené hrozby (podezřelá data, soubory, zaplavení, pakety, kontroly, spyware, adresy URL, viry, chyby zabezpečení, Wildfire-viry, wildfires). Další podrobnosti výstrahy najdete také v protokolu hrozeb Palo Alto, který odpovídá [typu hrozby nebo obsahu](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) uvedenému v popisu incidentu fúze.

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Podezřelé vzdálené spuštění služby WMI následovaný provozem neobvyklé označeným příznakem Palo Alto Networks firewall
Tento scénář je aktuálně ve **verzi Public Preview**.

**Mitre ATT&CK taktiku:** Spuštění, zjišťování

**Mitre technologie ATT&CK:** Rozhraní WMI (Windows Management Instrumentation) (T1047)

**Zdroje datových konektorů:** Microsoft Defender pro koncové body (dříve MDATP), Palo Alto Networks 

**Popis:** Incidenty fúze tohoto typu označují, že příkazy rozhraní WMI (Windows Management Interface) byly vzdáleně spuštěny v systému a že jsou zjištěny podezřelé příchozí aktivity bránou firewall Palo Alto Networks. To poskytuje indikaci, že útočník mohl získat přístup k vaší síti a pokouší se později přesunout, zvýšit oprávnění nebo spustit škodlivou datovou část. Stejně jako u všech "živých útoků" se může jednat o legitimní používání služby WMI. Nicméně vzdálené spuštění příkazu WMI následovaný podezřelou příchozí bránou firewall zvyšuje jistotu, že služba WMI je používána škodlivým způsobem a měla by být prozkoumána dále. V protokolech Palo Alto se Azure Sentinel zaměřuje na [protokoly hrozeb](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)a provoz se považuje za podezřelý, pokud jsou povolené hrozby (podezřelá data, soubory, zaplavení, pakety, kontroly, spyware, adresy URL, viry, chyby zabezpečení, Wildfire-viry, wildfires). Další podrobnosti výstrahy najdete také v protokolu hrozeb Palo Alto, který odpovídá [typu hrozby nebo obsahu](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) uvedenému v popisu incidentu fúze.

## <a name="malware-c2-or-download"></a>Malware C2 nebo stažení

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Požadavek na síť na službu pro samoobslužné zpracování dat následovaný provozem neobvyklé označeným příznakem Palo Alto Networks firewall.
Tento scénář je aktuálně ve **verzi Public Preview**.

**Mitre ATT&CK taktiku:** Příkaz a ovládací prvek

**Mitre technologie ATT&CK:** Šifrovaný kanál (T1573), proxy (T1090)

**Zdroje datových konektorů:** Microsoft Defender pro koncové body (dříve MDATP), Palo Alto Networks 

**Popis:** Incidenty fúze tohoto typu označují, že došlo k žádosti o odchozí připojení ke službě pro vzdálenou komunikaci, a za tímto účelem se zjistila příchozí aktivita neobvyklé bránou firewall Palo Alto Networks. To znamená, že útočník mohl získat přístup k vaší síti a snaží se zakrývat své akce a záměr. Připojení k síti systému pomocí tohoto modelu by mohla být indikace činnosti příkazu malwaru a řízení, požadavků na stažení dalšího malwaru nebo útočníka vytvářejícího vzdálený interaktivní přístup. V protokolech Palo Alto se Azure Sentinel zaměřuje na [protokoly hrozeb](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)a provoz se považuje za podezřelý, pokud jsou povolené hrozby (podezřelá data, soubory, zaplavení, pakety, kontroly, spyware, adresy URL, viry, chyby zabezpečení, Wildfire-viry, wildfires). Další podrobnosti výstrahy najdete také v protokolu hrozeb Palo Alto, který odpovídá [typu hrozby nebo obsahu](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) uvedenému v popisu incidentu fúze.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Odchozí připojení k IP adrese s historií neautorizovaných pokusů o přístup následovaných neobvyklé provozem označenými příznakem Palo Alto Networks firewall
Tento scénář je aktuálně ve **verzi Public Preview**.

**Mitre ATT&CK taktiku:** Příkaz a ovládací prvek

**Mitre technologie ATT&CK:** Nelze použít

**Zdroje datových konektorů:** Microsoft Defender pro koncové body (dříve MDATP), Palo Alto Networks 

**Popis:** Incidenty fúze tohoto typu označují, že odchozí připojení k IP adrese s historií neautorizovaných pokusů o přístup bylo navázáno, a za tímto účelem byla zjištěna aktivita neobvyklé bránou firewall sítě Palo Alto. Tím se zobrazí informace o tom, že útočník získal přístup k vaší síti. Počet pokusů o připojení, které následují tento model, by mohl být náznakem činnosti příkazu malware a řízení, požadavky na stažení dalšího malwaru nebo útočníka vytvářející vzdálený interaktivní přístup. V protokolech Palo Alto se Azure Sentinel zaměřuje na [protokoly hrozeb](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)a provoz se považuje za podezřelý, pokud jsou povolené hrozby (podezřelá data, soubory, zaplavení, pakety, kontroly, spyware, adresy URL, viry, chyby zabezpečení, Wildfire-viry, wildfires). Další podrobnosti výstrahy najdete také v protokolu hrozeb Palo Alto, který odpovídá [typu hrozby nebo obsahu](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) uvedenému v popisu incidentu fúze.

## <a name="ransomware"></a>ransomare,

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>Ransomwarem provádění po podezřelém přihlášení ke službě Azure AD

**Mitre ATT&CK taktiku:** Počáteční přístup, dopad

**Mitre technologie ATT&CK:** Platný účet (T1078), zašifrovaná data pro dopad (T1486)

**Zdroje datových konektorů:** Microsoft Cloud App Security Azure Active Directory Identity Protection

**Popis:** Incidenty fúze tohoto typu označují, že neobvyklé uživatelské chování indikující útok ransomwarem se zjistilo po podezřelém přihlášení k účtu Azure AD. Tato indikace poskytuje vysokou důvěru v tom, že účet uvedený v popisu incidentu fúze byl zneužit a byl použit k šifrování dat pro účely extorting vlastníka dat nebo odepření přístupu vlastníka dat ke svým datům. V upozorněních na spuštění ransomwarem jsou k dispermutaci podezřelá upozornění na přihlášení ke službě Azure AD:  

- **Nemožná cesta do neobvyklých míst, které vede k ransomwarem v cloudové aplikaci**

- **Přihlašovací událost z neznámého umístění, které vede k ransomwarem v cloudové aplikaci**

- **Přihlašovací událost z nakaženého zařízení, které se ransomwarem v cloudové aplikaci**

- **Přihlašovací událost z anonymní IP adresy vedoucí k ransomwarem v cloudové aplikaci**

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k ransomwarem v cloudové aplikaci**

## <a name="remote-exploitation"></a>Vzdálené využívání

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Podezření na použití architektury útoku, po kterém následují přenosy neobvyklé označené příznakem Palo Alto Networks firewall
Tento scénář je aktuálně ve **verzi Public Preview**.

**Mitre ATT&CK taktiku:** Počáteční přístup, provádění, příčný pohyb, eskalace oprávnění

**Mitre technologie ATT&CK:** Zneužití veřejné aplikace (T1190), zneužití pro spouštění klientů (T1203), využívání vzdálených služeb (T1210), využití pro eskalaci oprávnění (T1068)

**Zdroje datových konektorů:** Microsoft Defender pro koncové body (dříve MDATP), Palo Alto Networks 

**Popis:** Incidenty fúze tohoto typu označují, že nestandardní použití protokolů, které se podobá použití platforem útoku, jako je Metasploit, bylo zjištěno a následuje za tím, že v bráně firewall Palo Alto byly zjištěny podezřelé příchozí aktivity. Může se jednat o počáteční indikaci, že útočník zneužije službu k získání přístupu k síťovým prostředkům nebo že útočník už získal přístup a snaží se o další zneužití dostupných systémů a služeb k pozdějšímu přesunu nebo zvýšení oprávnění. V protokolech Palo Alto se Azure Sentinel zaměřuje na [protokoly hrozeb](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)a provoz se považuje za podezřelý, pokud jsou povolené hrozby (podezřelá data, soubory, zaplavení, pakety, kontroly, spyware, adresy URL, viry, chyby zabezpečení, Wildfire-viry, wildfires). Další podrobnosti výstrahy najdete také v protokolu hrozeb Palo Alto, který odpovídá [typu hrozby nebo obsahu](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) uvedenému v popisu incidentu fúze.

## <a name="next-steps"></a>Další kroky

Seznámili jste se s tím, že jste se dozvěděli o pokročilé detekci útoků ve více fázích, a můžete se zajímat, jak získat přehled o vašich datech a potenciálních hrozbách: [Začínáme s Sentinel Azure](quickstart-get-visibility.md).

Pokud jste připraveni prozkoumat incidenty, které jste si vytvořili, přečtěte si následující kurz: [Prozkoumejte incidenty pomocí Azure Sentinel](tutorial-investigate-cases.md).


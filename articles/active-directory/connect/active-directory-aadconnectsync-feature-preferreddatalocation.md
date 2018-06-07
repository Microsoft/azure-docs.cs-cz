---
title: 'Synchronizace služby Azure Active Directory Connect: konfigurujte upřednostňovaný data umístění pro geograficky více možností v Office 365 | Microsoft Docs'
description: Popisuje, jak uvést vaše prostředky uživatele služeb Office 365 blízko uživatele s Azure Active Directory Connect sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 5eae173e02c92bd43faaa9533ce29489d40f1389
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592921"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Synchronizace služby Azure Active Directory Connect: konfigurujte upřednostňovaný data umístění pro prostředky Office 365
Účelem tohoto tématu je pro vás provede procesem konfigurace atribut pro umístění upřednostňované dat v Azure Active Directory (Azure AD) připojit synchronizace. Když někdo používá více geograficky možnosti v Office 365, můžete použít k určení geografického umístění dat uživatele v Office 365 tento atribut. (Podmínky *oblast* a *geograficky* se zcela zaměnitelným významem používají.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Povolit synchronizaci umístění upřednostňované dat
Ve výchozím nastavení jsou Office 365 prostředky pro vaše uživatele nachází ve stejném geograficky redundantním jako klientovi Azure AD. Například pokud se váš klient nachází v Severní Americe, pak poštovní schránky Exchange uživatele jsou také umístěny v Severní Americe. Pro mezinárodní společnosti nemusí být optimální.

Nastavením atributu **preferredDataLocation**, můžete definovat geograficky uživatele. Můžete mít uživatele služeb Office 365 prostředky, například poštovní schránky a OneDrive, ve stejném geograficky redundantním jako uživatel a stále máte jednoho klienta pro celou organizaci.

> [!IMPORTANT]
> Více geograficky je aktuálně k dispozici zákazníkům s minimálně 5 000 předplatné služeb Office 365. Obraťte se na podrobnosti zástupce společnosti Microsoft.
>
>

Seznam všech oblastech pro Office 365 najdete v [kde je umístěný data?](https://aka.ms/datamaps).

Oblastech v Office 365, které jsou k dispozici pro více geograficky jsou:

| Zeměpisná oblast | Hodnota preferredDataLocation |
| --- | --- |
| Asie a Tichomoří | APC |
| Austrálie | AUSTRÁLIE |
| Kanada | MŮŽETE |
| Evropská unie | EUR |
| Indie | IND |
| Japonsko | JPN |
| Korea | KOR |
| Spojené království | GBR |
| Spojené státy | OSOBY |

* Pokud geograficky není uvedený v této tabulce (například Jižní Amerika), pak jej nelze použít pro více geograficky.
* Indie geograficky je pouze k dispozici pro zákazníky s fakturační adresu a v této geografické zakoupených licencí.
* Ne všechny úlohami Office 365 podporují použití nastavení geograficky uživatele.

### <a name="azure-ad-connect-support-for-synchronization"></a>Podporu Azure AD Connect pro synchronizaci

Azure AD Connect podporuje synchronizaci **preferredDataLocation** atribut pro **uživatele** objekty ve verzi 1.1.524.0 a novější. Zejména:

* Schéma typu objektu **uživatele** v konektor služby Azure AD je rozšířen o **preferredDataLocation** atribut. Atribut není typu řetězec s jednou hodnotou.
* Schéma typu objektu **osoba** v úložišti metaverse je rozšířen o **preferredDataLocation** atribut. Atribut není typu řetězec s jednou hodnotou.

Ve výchozím nastavení **preferredDataLocation** není povolena pro synchronizaci. Tato funkce je určena pro velké organizace. Také musíte určit atribut pro uložení geograficky Office 365 pro vaše uživatele, protože není žádná **preferredDataLocation** atribut v místní službě Active Directory. To se bude lišit pro každou organizaci.

> [!IMPORTANT]
> Azure AD umožňuje **preferredDataLocation** atributu u **cloudu uživatelské objekty** přímo nakonfigurovat pomocí Azure AD PowerShell. Azure AD umožňuje už **preferredDataLocation** atributu u **synchronizované uživatelské objekty** přímo nakonfigurovat pomocí Azure AD PowerShell. Ke konfiguraci tohoto atributu na **synchronizované uživatelské objekty**, je nutné použít Azure AD Connect.

Před povolením synchronizace:

* Rozhodněte, které místní služby Active Directory atribut má být použit jako zdrojový atribut. Musí být typu, **jednohodnotové řetězec**. V krocích, které následují, jeden z **extensionAttributes** se používá.
* Pokud jste dříve nakonfigurovali **preferredDataLocation** atribut na existující **synchronizované uživatelské objekty** ve službě Azure AD pomocí Azure AD PowerShell, je nutné backport atribut hodnoty k odpovídající **uživatele** objekty v místní službě Active Directory.

    > [!IMPORTANT]
    > V takovém případě není backport tyto hodnoty Azure AD Connect Odebere existující hodnoty atributu ve službě Azure AD při synchronizaci **preferredDataLocation** atribut je povolen.

* Nakonfigurujte zdrojový atribut na alespoň několik objektů místního uživatele služby Active Directory. Můžete použít pro ověření později.

V následujících částech najdete postup povolení synchronizace **preferredDataLocation** atribut.

> [!NOTE]
> Kroky jsou popsané v rámci nasazení služby Azure AD s jednou doménovou strukturou topologie a bez vlastní synchronizační pravidla. Pokud máte topologie s více doménovými strukturami, nakonfigurovat vlastní synchronizační pravidla, nebo mají na testovacím serveru, by měl odpovídajícím způsobem upravit kroky.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1: Zakázat plánovače synchronizace a ověřte, že neexistuje žádná synchronizace v průběhu
Aby se zabránilo neúmyslnému změny se exportují do služby Azure AD, ujistěte se, že žádná synchronizace bude probíhat, když jste právě aktualizují se pravidla synchronizace. Postup při zakázání plánovače předdefinované synchronizace:

1. Spusťte relaci prostředí PowerShell na server Azure AD Connect.
2. Zakázat plánované synchronizace spuštěním této rutiny: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Spuštění **Synchronization Service Manager** přechodem na **spustit** > **synchronizační služba**.
4. Vyberte **operace** a zkontrolujte neprobíhá žádná operace se stavem *v průběhu*.

![Snímek obrazovky portálu Service Manager synchronizace](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Krok 2: Přidání zdrojového atributu schématu místní konektor služby Active Directory
Všechny atributy Azure AD jsou importovány do prostoru konektoru služby Active Directory v místě. Pokud jste vybrali do atribut, který není synchronizován se ve výchozím nastavení použít, budete muset importovat. Zdrojový atribut přidat do seznamu importované atributy:

1. Vyberte **konektory** kartě Synchronization Service Manager.
2. Klikněte pravým tlačítkem na konektor místní služby Active Directory a vyberte **vlastnosti**.
3. V dialogovém okně automaticky otevírané okno přejděte do **vybrat atributy** kartě.
4. Zkontrolujte, že je zaškrtnuté zdrojový atribut, který Pokud chcete použít v seznamu atributů. Pokud nevidíte požadovaný atribut, vyberte **Zobrazit vše** zaškrtávací políčko.
5. Chcete-li uložit, vyberte **OK**.

![Dialogové okno snímek obrazovky Synchronization Service Manager a vlastnosti](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Krok 3: Přidání **preferredDataLocation** schématu Azure AD Connector.
Ve výchozím nastavení **preferredDataLocation** atribut není importován do prostoru konektoru Azure AD. Přidejte do seznamu importované atributy:

1. Vyberte **konektory** kartě Synchronization Service Manager.
2. Klikněte pravým tlačítkem na konektoru Azure AD a vyberte **vlastnosti**.
3. V dialogovém okně automaticky otevírané okno přejděte do **vybrat atributy** kartě.
4. Vyberte **preferredDataLocation** atribut v seznamu.
5. Chcete-li uložit, vyberte **OK**.

![Dialogové okno snímek obrazovky Synchronization Service Manager a vlastnosti](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule"></a>Krok 4: Vytvoření pravidla synchronizace příchozích dat
Pravidla synchronizace příchozích dat umožňuje hodnotu atributu mají být předány z atributu zdroje ve službě Active Directory místní úložiště metaverse.

1. Spuštění **editoru pravidel synchronizace** přechodem na **spustit** > **editoru pravidel synchronizace**.
2. Nastavit filtr hledání **směr** být **příchozí**.
3. Chcete-li vytvořit nové příchozí pravidlo, vyberte **přidat nové pravidlo**.
4. V části **popis** kartě, zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | --- | --- | --- |
    | Název | *Zadejte název* | Například "v ze služby Active Directory – uživatel preferredDataLocation" |
    | Popis | *Zadejte vlastní popis* |  |
    | Připojený systém | *Vyberte místní konektor služby Active Directory* |  |
    | Typ objektu systému připojené | **Uživatel** |  |
    | Typ objektu úložiště Metaverse | **Osoba** |  |
    | Typ propojení | **Spojení** |  |
    | Priorita | *Vyberte číslo mezi 1 – 99* | 1 – 99 je vyhrazený pro vlastní synchronizačního pravidla. Není vyberte hodnotu, která je používána jinou synchronizační pravidlo. |

5. Zachovat **Scoping filtru** prázdná, chcete-li zahrnout všechny objekty. Možná budete muset upravit oboru filtru, podle vašeho nasazení Azure AD Connect.
6. Přejděte na **transformace karta**a implementovat následující pravidla transformace:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Merge – typ |
    | --- | --- | --- | --- | --- |
    |Přímé | PreferredDataLocation | Vyberte zdrojový atribut | Nezaškrtnuto | Aktualizace |

7. Chcete-li vytvořit příchozí pravidlo, vyberte **přidat**.

![Snímek obrazovky vytvoření pravidla synchronizace příchozích dat](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule"></a>Krok 5: Vytvoření pravidlo odchozí synchronizace
Pravidlo odchozí synchronizace umožňuje hodnotu atributu z úložiště metaverse na tok **preferredDataLocation** atributu ve službě Azure AD:

1. Přejděte na **synchronizace pravidla Editor**.
2. Nastavit filtr hledání **směr** být **odchozí**.
3. Vyberte **přidat nové pravidlo**.
4. V části **popis** kartě, zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | ----- | ------ | --- |
    | Název | *Zadejte název* | Například "Out do služby Azure AD – uživatel preferredDataLocation" |
    | Popis | *Zadejte popis* ||
    | Připojený systém | *Vyberte konektoru služby Azure AD* ||
    | Typ objektu systému připojené | **Uživatel** ||
    | Typ objektu úložiště Metaverse | **Osoba** ||
    | Typ propojení | **Spojení** ||
    | Priorita | *Vyberte číslo mezi 1 – 99* | 1 – 99 je vyhrazený pro vlastní synchronizačního pravidla. Není vyberte hodnotu, která je používána jinou synchronizační pravidlo. |

5. Přejděte na **Scoping filtru** a přidejte jednu oboru filtru skupinu se dvěma klauzule:

    | Atribut | Operátor | Hodnota |
    | --- | --- | --- |
    | sourceObjectType | ROVNÁ | Uživatel |
    | cloudMastered | NOTEQUAL | True |

    Určuje oboru filtru, které objekty Azure AD, že je toto pravidlo odchozí synchronizace použito pro. V tomto příkladu používáme stejné oboru filtru z "Na více systémů do AD – identitu uživatele" OOB (out-of-box) synchronizační pravidlo. Pravidlo synchronizace zabrání aplikované na **uživatele** objekty, které nejsou synchronizovány z místní služby Active Directory. Možná budete muset upravit oboru filtru, podle vašeho nasazení Azure AD Connect.

6. Přejděte na **transformace** kartě a implementovat následující pravidla transformace:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Merge – typ |
    | --- | --- | --- | --- | --- |
    | Přímé | PreferredDataLocation | PreferredDataLocation | Nezaškrtnuto | Aktualizace |

7. Zavřít **přidat** vytvoření odchozí pravidla.

![Snímek obrazovky vytvořit pravidlo odchozí synchronizace](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>Krok 6: Spusťte úplnou synchronizaci cyklus
Obecně platí cyklus úplná synchronizace je požadovaná. Je to proto, že máte přidají nové atributy služby Active Directory i schématu konektoru služby Azure AD a zavedená vlastní synchronizační pravidla. Před exportováním je do Azure AD ověřte změny. Následující postup vám pomůže ověřit změny, při spuštění ručně kroky, které tvoří cyklus úplnou synchronizaci.

1. Spustit **úplný import** na konektor místní služby Active Directory:

   1. Přejděte na **Operations** kartě Synchronization Service Manager.
   2. Klikněte pravým tlačítkem myši **místní konektor služby Active Directory**a vyberte **spustit**.
   3. V dialogovém okně vyberte **úplný Import**a vyberte **OK**.
   4. Počkejte na dokončení operace.

    > [!NOTE]
    > Pokud zdrojový atribut je již zahrnut v seznamu importované atributy, můžete přeskočit úplný import na konektor místní služby Active Directory. Jinými slovy můžete nebylo nutné provést změnu během kroku 2 výše v tomto článku.

2. Spustit **úplný import** na konektor služby Azure AD:

   1. Klikněte pravým tlačítkem myši **konektoru služby Azure AD**a vyberte **spustit**.
   2. V dialogovém okně vyberte **úplný Import**a vyberte **OK**.
   3. Počkejte na dokončení operace.

3. Ověření změny pravidel synchronizace na existujícím **uživatele** objektu.

   Zdrojový atribut ze služby Active Directory v místě a **preferredDataLocation** z Azure AD, byly naimportovány do každého prostoru příslušného konektoru. Než budete pokračovat v kroku úplnou synchronizaci, proveďte náhled na existujícím **uživatele** objekt v prostoru místní konektor služby Active Directory. Objekt, který jste vybrali měli zdrojový atribut naplněno. Úspěšné preview s **preferredDataLocation** vložené do úložiště metaverse je dobré indikátoru, které jste nakonfigurovali synchronizaci pravidla správně. Informace o tom, jak provést náhled, najdete v článku [změnu ověřit](active-directory-aadconnectsync-change-the-configuration.md#verify-the-change).

4. Spustit **úplné synchronizace** na místní konektor služby Active Directory:

   1. Klikněte pravým tlačítkem myši **místní konektor služby Active Directory**a vyberte **spustit**.
   2. V dialogovém okně vyberte **úplná synchronizace**a vyberte **OK**.
   3. Počkejte na dokončení operace.

5. Ověřte **čekajících exportů** do služby Azure AD:

   1. Klikněte pravým tlačítkem myši **konektoru služby Azure AD**a vyberte **hledání prostoru konektoru**.
   2. V **hledání prostoru konektoru** dialogové okno:

        a. Nastavit **oboru** k **čekající na vyřízení Export**.<br>
        b. Zaškrtněte všechny tři políčka, včetně **přidat, upravit a odstranit**.<br>
        c. Chcete-li zobrazit seznam objektů se změnami pro export, vyberte **vyhledávání**. Chcete-li zkontrolovat změny pro daný objekt, dvakrát klikněte na objekt.<br>
        d. Ověřte, že se očekává změny.

6. Spustit **exportovat** na **konektoru služby Azure AD**

   1. Klikněte pravým tlačítkem myši **konektoru služby Azure AD**a vyberte **spustit**.
   2. V **spustit konektor** dialogové okno, vyberte **exportovat**a vyberte **OK**.
   3. Počkejte na dokončení operace.

> [!NOTE]
> Můžete si všimnout, že kroky nezahrnují krok úplná synchronizace v konektoru Azure AD ani kroku export na konektoru služby Active Directory. Kroky nejsou povinné, protože jsou hodnoty atributu odesílaných z místní služby Active Directory do Azure AD pouze.

## <a name="step-7-re-enable-sync-scheduler"></a>Krok 7: Opětovné povolení plánovače synchronizace
Znovu povolte plánovače předdefinované synchronizace:

1. Spusťte relaci prostředí PowerShell.
2. Plánované synchronizace znovu povolte spuštěním této rutiny: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>Krok 8: Ověřit výsledek
Nyní je čas ověřte konfiguraci a povolení pro vaše uživatele.

1. Geografické přidáte do vybraného atributu na uživatele. Seznam dostupných oblastech najdete v [tuto tabulku](#enable-synchronization-of-preferreddatalocation).  
![Snímek obrazovky atributů AD přidat na uživatele](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Počkejte, než pro atribut synchronizovány se službou Azure AD.
3. Pomocí Exchange Online Powershellu ověřte správně nastavené oblasti poštovní schránky.  
![Snímek obrazovky Exchange Online Powershellu](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Za předpokladu, že váš klient je označená mohli tuto funkci používat, je poštovní schránka přesunuta do správné geograficky. To můžete ověřit pohledem na název serveru, kde se nachází poštovní schránku.
4. Pokud chcete ověřit, že toto nastavení bylo efektivní přes kolik poštovních schránek, použijte skript v [Galerii TechNet](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e). Tento skript má také seznam předpon serveru všech datových center Office 365, a které geograficky se nachází v. Můžete použít jako referenci v předchozím kroku ověření umístění poštovní schránku.

## <a name="next-steps"></a>Další postup

Další informace o více geograficky v Office 365:

* [Geograficky více relací na Ignite](https://aka.ms/MultiGeoIgnite)
* [Více geograficky ve Onedrivu](https://aka.ms/OneDriveMultiGeo)
* [Více geograficky ve službě SharePoint Online](https://aka.ms/SharePointMultiGeo)

Další informace o konfiguraci modelu v synchronizační modul:

* Další informace o konfiguraci modelu v [Principy deklarativní zřizování](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Další informace o jazyk výrazů v [Principy deklarativní zřizování výrazy](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

Témata s přehledem:

* [Synchronizace Azure AD Connect: pochopení a přizpůsobení synchronizace](active-directory-aadconnectsync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)

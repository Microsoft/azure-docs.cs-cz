---
title: 'Synchronizace služby Azure Active Directory Connect: Konfigurace upřednostňované umístění dat pro možnosti geografickým oblastem v Office 365 | Dokumentace Microsoftu'
description: Popisuje, jak uvést vaše prostředky uživatele Office 365 umístěný blízko uživatele pomocí služby Azure Active Directory Connect sync.
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
ms.date: 07/30/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 951ac39e1bb6ce050da26fa8ff3f93fd4e27e7d7
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318382"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Synchronizace služby Azure Active Directory Connect: Konfigurace upřednostňované umístění dat pro prostředky služeb Office 365
Účelem tohoto tématu je pro vás provedou postupem konfigurace atribut upřednostňované umístění dat ve službě Azure Active Directory (Azure AD) Connect sync. Při použití možnosti geografickým oblastem v Office 365, můžete použít tento atribut k určení geografické umístění dat uživatele v Office 365. (Podmínky *oblasti* a *geograficky* zaměňují.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Povolení synchronizace hodnot upřednostňované umístění dat
Ve výchozím nastavení Office 365 prostředky pro vaše uživatele jsou umístěny ve stejné zeměpisné oblasti jako váš tenant Azure AD. Například pokud váš tenant nachází v Severní Americe, pak poštovní schránky Exchange uživatele jsou také umístěny v Severní Americe. Pro mezinárodní organizace nemusí být optimální.

Nastavením atributu **preferredDataLocation**, můžete definovat geograficky uživatele. Můžete mít uživatele Office 365 prostředky, jako jsou poštovní schránka a Onedrivu, v rámci stejné Geografie jako uživatel a stále máte jednoho tenanta pro celou organizaci.

> [!IMPORTANT]
> Geografickým oblastem je momentálně dostupný pro zákazníky s minimálně 2 500 předplatné služeb Office 365. Obraťte se prosím na zástupce společnosti Microsoft pro podrobnosti.
>
>

Seznam všech zeměpisných oblastech pro Office 365 najdete v [kde jsou vaše data umístěné?](https://aka.ms/datamaps).

Se používají Geografie v Office 365, které jsou k dispozici pro geografickým oblastem:

| Zeměpisná oblast | Hodnota preferredDataLocation |
| --- | --- |
| Asie a Tichomoří | APC |
| Austrálie | JEDNOTEK AU |
| Kanada | MŮŽETE |
| Evropská unie | EUR |
| Francie | SADA FRA |
| Indie | IND |
| Japonsko | SADA JPN |
| Jižní Korea | KOR |
| Spojené království | GBR |
| Spojené státy | NÁZEV |

* Pokud zeměpisné oblasti není uveden v této tabulce (například Jižní Amerika), pak jej nelze použít pro geografickým oblastem.
* Zeměpisné oblasti Indie je pouze dostupné zákazníkům s fakturační adresu a v této geografické zakoupených licencí k agentům.
* Ne všechny úlohy Office 365 nepodporují nastavení geografické uživatele.

### <a name="azure-ad-connect-support-for-synchronization"></a>Podpora Azure AD Connect pro synchronizaci

Azure AD Connect podporuje synchronizaci **preferredDataLocation** atribut pro **uživatele** objektů verze 1.1.524.0 nebo novější. Zejména:

* Schéma typu objektu **uživatele** v konektoru Azure AD je rozšířit **preferredDataLocation** atribut. Atribut je typu, jedinou hodnotu řetězce.
* Schéma typu objektu **osoba** v úložišti metaverse je rozšířit **preferredDataLocation** atribut. Atribut je typu, jedinou hodnotu řetězce.

Ve výchozím nastavení **preferredDataLocation** není povolená pro synchronizaci. Tato funkce je určená pro velké organizace. Atribut pro uložení geograficky Office 365 pro uživatele, musíte také identifikovat, protože neexistuje žádný **preferredDataLocation** atribut v místní službě Active Directory. To se bude lišit pro každou organizaci.

> [!IMPORTANT]
> Azure AD umožňuje **preferredDataLocation** atribut na **cloudové uživatelské objekty** přímo konfigurovat pomocí Azure AD PowerShell. Azure AD umožňuje už **preferredDataLocation** atribut na **synchronizované uživatelské objekty** přímo konfigurovat pomocí Azure AD PowerShell. Ke konfiguraci tohoto atributu na **synchronizované uživatelské objekty**, je nutné použít Azure AD Connect.

Než povolíte synchronizaci:

* Rozhodněte, které místní služby Active Directory atribut má být použit jako zdrojový atribut. By měl být typu, **jednohodnotové řetězec**. V krocích, které následují, jeden z **extensionAttributes** se používá.
* Pokud jste dříve nakonfigurovali **preferredDataLocation** atribut ve stávajících **synchronizované uživatelské objekty** ve službě Azure AD pomocí Azure AD Powershellu, musíte backport hodnotu atributu odpovídající **uživatele** objekty v místní službě Active Directory.

    > [!IMPORTANT]
    > Pokud neprovedete tyto hodnoty nejsou backport, Azure AD Connect odebere stávající hodnoty atributů ve službě Azure AD při synchronizaci pro **preferredDataLocation** atribut je povolen.

* Konfigurace zdrojového atributu na alespoň několik objektů místní uživatele Active Directory. Může být využit k ověření později.

Následující části popisují postup povolení synchronizace hodnot **preferredDataLocation** atribut.

> [!NOTE]
> Kroky jsou popsány v rámci nasazení služby Azure AD s jednou doménovou strukturou topologie a bez vlastní synchronizační pravidla. Pokud máte topologie s více doménovými strukturami, vlastní synchronizační pravidla nakonfigurované nebo mít na testovacím serveru, byste měli odpovídajícím způsobem upravit kroky.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1: Zakázat Plánovač synchronizace a ověřte, že není žádná probíhající synchronizace
Aby se zabránilo neúmyslnému změny exportované do služby Azure AD, ujistěte se, že žádná synchronizace bude probíhat, když jste uprostřed aktualizují se pravidla synchronizace. Chcete-li zakázat plánovač integrované synchronizace:

1. Spusťte relaci Powershellu na serveru služby Azure AD Connect.
2. Zakažte plánované synchronizaci spuštěním této rutiny: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Spustit **Synchronization Service Manager** tak, že přejdete do **START** > **synchronizační služba**.
4. Vyberte **operace** kartu a potvrdit, neexistuje žádná operace se stavem *probíhá*.

![Snímek obrazovky portálu Service Manager synchronizace](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Krok 2: Přidání zdrojového atributu schématu místní konektor služby Active Directory
Ne všechny atributy služby Azure AD jsou importovány do místního prostoru konektoru služby Active Directory. Pokud vyberete použití atributu, který není synchronizován ve výchozím nastavení, musíte ho naimportovat. Přidání zdrojového atributu na seznam importovaných atributy:

1. Vyberte **konektory** kartu v Synchronization Service Manager.
2. Klikněte pravým tlačítkem na konektor pro místní Active Directory a vyberte **vlastnosti**.
3. V místním dialogovém, přejděte **vybrat atributy** kartu.
4. Ujistěte se, že je zaškrtnuto zdrojový atribut, který jste se rozhodli používat v seznamu atributů. Pokud nevidíte požadovaný atribut, vyberte **Zobrazit vše** zaškrtávací políčko.
5. Chcete-li uložit, vyberte **OK**.

![Dialogové okno snímek obrazovky Synchronization Service Manager a vlastnosti](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Krok 3: Přidání **preferredDataLocation** schématu konektor služby Azure AD
Ve výchozím nastavení **preferredDataLocation** atribut není importován do prostoru konektoru Azure AD. Přidejte do seznamu importované atributů:

1. Vyberte **konektory** kartu v Synchronization Service Manager.
2. Klikněte pravým tlačítkem na konektor služby Azure AD a vyberte **vlastnosti**.
3. V místním dialogovém, přejděte **vybrat atributy** kartu.
4. Vyberte **preferredDataLocation** atributem v seznamu.
5. Chcete-li uložit, vyberte **OK**.

![Dialogové okno snímek obrazovky Synchronization Service Manager a vlastnosti](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule"></a>Krok 4: Vytvoření pravidla synchronizace příchozích dat
Pravidlo příchozí synchronizace povoluje hodnotu atributu přejít ze zdrojového atributu v místní službě Active Directory do úložiště metaverse.

1. Spustit **Editor pravidel synchronizace** tak, že přejdete do **START** > **Editor pravidel synchronizace**.
2. Nastavení filtru hledání **směr** bude **příchozí**.
3. Chcete-li vytvořit nové příchozí pravidlo, vyberte **přidat nové pravidlo**.
4. V části **popis** kartu, zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | --- | --- | --- |
    | Název | *Zadejte název* | Například "v ze služby AD – uživatel preferredDataLocation" |
    | Popis | *Zadejte vlastní popis* |  |
    | Připojený systém | *Vyberte konektor služby Active Directory on-premises* |  |
    | Typ objektu systému připojené | **Uživatel** |  |
    | Typ objektu úložiště Metaverse | **Osoba** |  |
    | Typ odkazu | **Spojení** |  |
    | Priorita | *Zvolte číslo v rozmezí 1 – 99* | 1 – 99 je vyhrazený pro vlastní synchronizační pravidla. Nelze vybrat hodnotu, která používá dalším synchronizačním pravidle. |

5. Zachovat **Scoping filtr** prázdný, chcete-li zahrnout všechny objekty. Jste možná bude nutné upravit filtr oborů podle vašeho nasazení služby Azure AD Connect.
6. Přejděte **transformace kartu**a implementovat následující pravidla transformace:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Sloučit typu |
    | --- | --- | --- | --- | --- |
    |Přímé | preferredDataLocation | Vyberte zdrojový atribut | Není zaškrtnuto | Aktualizace |

7. Chcete-li vytvořit příchozí pravidlo, vyberte **přidat**.

![Snímek obrazovky vytvoření pravidla synchronizace příchozích dat](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule"></a>Krok 5: Vytvoření pravidla odchozí synchronizace
Pravidlo odchozí synchronizace povoluje hodnotu atributu z úložiště metaverse na tok **preferredDataLocation** atributu ve službě Azure AD:

1. Přejděte **Editor synchronizačních pravidel**.
2. Nastavení filtru hledání **směr** bude **odchozí**.
3. Vyberte **přidat nové pravidlo**.
4. V části **popis** kartu, zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | ----- | ------ | --- |
    | Název | *Zadejte název* | Například "Out do služby Azure AD – uživatel preferredDataLocation" |
    | Popis | *Zadejte popis* ||
    | Připojený systém | *Vyberte konektor Azure AD* ||
    | Typ objektu systému připojené | **Uživatel** ||
    | Typ objektu úložiště Metaverse | **Osoba** ||
    | Typ odkazu | **Spojení** ||
    | Priorita | *Zvolte číslo v rozmezí 1 – 99* | 1 – 99 je vyhrazený pro vlastní synchronizační pravidla. Nelze vybrat hodnotu, která používá dalším synchronizačním pravidle. |

5. Přejděte **Scoping filtr** karta a přidat jednu skupinu filtr oborů s dvou klauzulí:

    | Atribut | Operátor | Hodnota |
    | --- | --- | --- |
    | sourceObjectType | ROVNO | Uživatel |
    | cloudMastered | NOTEQUAL | True |

    Určuje filtr oborů, které toto pravidlo odchozí synchronizace se použije pro objekty Azure AD. V tomto příkladu použijeme stejný filtr oborů z "Na více instancí AD – identita uživatele" OOB (out-of-box) synchronizačního pravidla. To brání použití pro synchronizační pravidlo **uživatele** objekty, které nejsou synchronizované z místní služby Active Directory. Jste možná bude nutné upravit filtr oborů podle vašeho nasazení služby Azure AD Connect.

6. Přejděte **transformace** kartu a implementovat následující pravidla transformace:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Sloučit typu |
    | --- | --- | --- | --- | --- |
    | Přímé | preferredDataLocation | preferredDataLocation | Není zaškrtnuto | Aktualizace |

7. Zavřít **přidat** vytvoření odchozí pravidla.

![Snímek obrazovky vytvořit pravidlo odchozí synchronizace](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>Krok 6: Spuštění cyklu úplné synchronizace
Obecně platí úplná synchronizace cyklus je povinný. Toto je vzhledem k tomu, že jsme přidali nové atributy služby Active Directory a Azure AD Connector schématu a zavedl vlastní synchronizační pravidla. Ověřte změny před exportem do služby Azure AD. Následující kroky můžete ověřit změny, při ručním spuštěním kroky, které tvoří úplnou synchronizační cyklus.

1. Spustit **úplný import** na místní konektoru služby Active Directory:

   1. Přejděte **operace** kartu v Synchronization Service Manager.
   2. Klikněte pravým tlačítkem myši **místní konektor služby Active Directory**a vyberte **spustit**.
   3. V dialogovém okně vyberte **úplný Import**a vyberte **OK**.
   4. Počkejte na dokončení operace.

    > [!NOTE]
    > Pokud zdrojový atribut je již součástí seznam importovaných atributy, můžete přeskočit úplný import na místní konektoru služby Active Directory. Jinými slovy můžete není nutné provádět změny během kroku 2 výše v tomto článku.

2. Spustit **úplný import** v konektoru služby Azure AD:

   1. Klikněte pravým tlačítkem myši **konektor služby Azure AD**a vyberte **spustit**.
   2. V dialogovém okně vyberte **úplný Import**a vyberte **OK**.
   3. Počkejte na dokončení operace.

3. Ověření změny pravidel synchronizace na existujícím **uživatele** objektu.

   Zdrojový atribut ze služby Active Directory s místními a **preferredDataLocation** ze služby Azure AD se naimportovaly do každého prostoru příslušný konektor. Než budete pokračovat s krokem úplnou synchronizaci provést náhled na existujícím **uživatele** objekt v prostoru místní konektor služby Active Directory. Objekt, který jste vybrali by měl mít zdrojový atribut naplnit. Úspěšné ve verzi preview s **preferredDataLocation** v úložišti metaverse je vhodný indikátor, že nakonfigurujete synchronizaci pravidla správně. Informace o tom, jak provést ve verzi preview najdete v tématu [zkontrolujte změnu](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Spustit **úplné synchronizace** na konektor on-premises služby Active Directory:

   1. Klikněte pravým tlačítkem myši **místní konektor služby Active Directory**a vyberte **spustit**.
   2. V dialogovém okně vyberte **úplnou synchronizaci**a vyberte **OK**.
   3. Počkejte na dokončení operace.

5. Ověřte **čekajících exportů** do služby Azure AD:

   1. Klikněte pravým tlačítkem myši **konektor služby Azure AD**a vyberte **Search Connector Space**.
   2. V **Search Connector Space** dialogové okno:

        a. Nastavte **oboru** k **čekající na Export**.<br>
        b. Zaškrtněte všechna tři políčka, včetně **přidat, upravit a odstranit**.<br>
        c. Chcete-li zobrazit seznam objektů se změnami, které mají být exportovány, vyberte **hledání**. Přezkoumat změny pro daný objekt, dvakrát klikněte na objekt.<br>
        d. Ověřte, že jsou očekávané změny.

6. Spustit **exportovat** na **konektoru služby Azure AD**

   1. Klikněte pravým tlačítkem myši **konektor služby Azure AD**a vyberte **spustit**.
   2. V **spuštění konektoru** dialogu **exportovat**a vyberte **OK**.
   3. Počkejte na dokončení operace.

> [!NOTE]
> Můžete si všimnout, že kroky neobsahují krok úplná synchronizace v konektoru služby Azure AD, nebo krok export na konektoru služby Active Directory. Kroky nejsou povinné, protože hodnoty atributů jsou vyplývající z místní služby Active Directory do služby Azure AD pouze.

## <a name="step-7-re-enable-sync-scheduler"></a>Krok 7: Opětovné povolení Plánovač synchronizace
Povolte Plánovač integrované synchronizace:

1. Spusťte relaci Powershellu.
2. Plánované synchronizaci znovu povolte spuštěním této rutiny: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>Krok 8: Ověření výsledku
Nyní je čas potřebný k ověření konfigurace nebo ji povolit pro vaše uživatele.

1. Přidáte vybraný atribut uživatele zeměpisné oblasti. Seznam dostupných zeměpisných oblastech najdete v [Tato tabulka](#enable-synchronization-of-preferreddatalocation).  
![Snímek obrazovky atribut AD přidat uživatele](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Počkejte, atributy, které mají být synchronizovány do Azure AD.
3. Pomocí Powershellu v Exchangi Online, ověřte, že oblast poštovní schránky byla nastavena správně.  
![Snímek obrazovky Powershellu v Exchangi Online](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Za předpokladu, že byl označen tenanta mohli tuto funkci používat, poštovní schránku se přesune do správné zeměpisné oblasti. Můžete to ověřit pohledem na název serveru, kde se nachází poštovní schránku.
4. Pokud chcete ověřit, že toto nastavení bylo efektivní za kolik poštovních schránek, použijte skript v [Galerie TechNet](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e). Tento skript má také seznam předpon serveru ze všech datových center Office 365, a které geografické se nachází v. Můžete použít jako referenci v předchozím kroku ověření umístění poštovní schránky.

## <a name="next-steps"></a>Další postup

Další informace o geografickým oblastem v Office 365:

* [Geografickým oblastem semináře na konferenci Ignite](https://aka.ms/MultiGeoIgnite)
* [Geografickým oblastem ve Onedrivu](https://aka.ms/OneDriveMultiGeo)
* [Geografickým oblastem v Sharepointu Online](https://aka.ms/SharePointMultiGeo)

Další informace o model konfigurace v modulu synchronizace:

* Další informace o konfiguraci modelu v [Principy deklarativní zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Další informace o jazyk výrazů v [Principy výrazů deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Témata s přehledem:

* [Synchronizace Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

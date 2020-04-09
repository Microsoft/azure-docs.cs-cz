---
title: 'Azure AD Connect: Konfigurace umístění upřednostňovaných dat pro prostředky Office 365'
description: Popisuje, jak umístit vaše uživatelské prostředky Office 365 blízko k uživateli pomocí synchronizace Azure Active Directory Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 723411191d0990583d039a0fc9651437480807b4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983258"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Synchronizace služby Azure Active Directory Connect: Konfigurace umístění upřednostňovaných dat pro prostředky Office 365
Účelem tohoto tématu je vás provede, jak nakonfigurovat atribut pro umístění upřednostňovaných dat v Azure Active Directory (Azure AD) Connect synchronizace. Když někdo používá funkce Multi-Geo v Office 365, použijte tento atribut k určení geografického umístění dat Office 365 uživatele. (Termíny *region* a *geo* se používají zaměnitelně.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Povolit synchronizaci upřednostňovaného umístění dat
Ve výchozím nastavení jsou prostředky Office 365 pro vaše uživatele umístěné ve stejné geografické oblasti jako váš klient Azure AD. Pokud se například váš tenant nachází v Severní Americe, poštovní schránky serveru Exchange uživatelů se také nacházejí v Severní Americe. Pro nadnárodní organizaci to nemusí být optimální.

Nastavením atributu **preferredDataLocation**můžete definovat geo uživatele. Můžete mít prostředky office 365 uživatele, jako je poštovní schránka a OneDrive, ve stejné zeměpisné oblasti jako uživatel a stále máte jednoho klienta pro celou organizaci.

> [!IMPORTANT]
> Multi-Geo je v současné době k dispozici zákazníkům s aktivní smlouvou Enterprise a minimálně 500 předplatným služeb Office 365. Podrobnosti vám sdělte se zástupcem společnosti Microsoft.
>
>

Seznam všech geos pro Office 365 najdete v části [Kde se nacházejí vaše data?](https://aka.ms/datamaps).

Geos v Office 365 dostupné pro Multi-Geo jsou:

| Geografická oblast | preferredDataLocation hodnota |
| --- | --- |
| Asie a Tichomoří | Apc |
| Austrálie | Aus |
| Kanada | Cna |
| Evropská unie | EUR |
| Francie | FRA |
| Indie | IND |
| Japonsko | JPN |
| Jižní Korea | KOR |
| Jižní Afrika | ZAF |
| Švýcarsko | Che |
| Spojené arabské emiráty | Jsou |
| Spojené království | Gbr |
| Spojené státy | Nam |

* Pokud geo není uveden v této tabulce (například Jižní Amerika), pak nemůže být použit pro Multi-Geo.

* Ne všechny úlohy Office 365 podporují nastavení geografické polohy uživatele.

### <a name="azure-ad-connect-support-for-synchronization"></a>Podpora Azure AD Connect pro synchronizaci

Azure AD Connect podporuje synchronizaci **atributu preferredDataLocation** pro **uživatelské** objekty ve verzi 1.1.524.0 a novější. Konkrétně:

* Schéma typu objektu **Uživatel** v konektoru Azure AD je rozšířeno tak, aby zahrnovalo atribut **preferredDataLocation.** Atribut je typu, řetězec s jednou hodnotou.
* Schéma typu objektu **Osoba** v metaverse je rozšířena tak, aby zahrnovala **atribut preferredDataLocation.** Atribut je typu, řetězec s jednou hodnotou.

Ve výchozím nastavení není **preferredDataLocation** povoleno pro synchronizaci. Tato funkce je určena pro větší organizace. Schéma služby Active Directory v systému Windows Server 2019 obsahuje atribut **msDS preferredDataLocation,** který byste měli pro tento účel použít. Pokud jste neaktualizovali schéma služby Active Directory a nemůžete tak učinit, musíte určit atribut, který bude pro uživatele obsahovat geografickou funkci Office 365. Pro každou organizaci to bude jiné.

> [!IMPORTANT]
> Azure AD umožňuje upřednostňovaný atribut **DataLocation** v **cloudových objektech Uživatele** přímo nakonfigurovat pomocí Prostředí Azure AD PowerShell. Chcete-li nakonfigurovat tento atribut na **synchronizované objekty uživatele**, je nutné použít Azure AD Connect.

Před povolením synchronizace:

* Pokud jste schéma služby Active Directory neupgradovali na 2019, rozhodněte se, který místní atribut služby Active Directory se má použít jako zdrojový atribut. Měl by být typu, **single-valued řetězec**.
* Pokud jste dříve nakonfigurovali atribut **preferredDataLocation** u **existujících synchronizovaných uživatelských objektů** ve službě Azure AD pomocí prostředí Azure AD PowerShell, musíte přenést hodnoty atributů na odpovídající objekty **uživatele** v místním adresáři Active Directory.

    > [!IMPORTANT]
    > Pokud tyto hodnoty nevytesáte zpět, Azure AD Connect odebere existující hodnoty atributů ve službě Azure AD, když je povolena synchronizace atributu **preferredDataLocation.**

* Nakonfigurujte zdrojový atribut alespoň u několika místních objektů uživatele služby Active Directory. Můžete použít pro ověření později.

Následující části poskytují kroky k povolení synchronizace **atributu preferredDataLocation.**

> [!NOTE]
> Kroky jsou popsány v kontextu nasazení Azure AD s topologii jedné doménové struktury a bez vlastních pravidel synchronizace. Pokud máte topologii s více doménovými strukturami, nakonfigurovaná vlastní pravidla synchronizace nebo máte pracovní server, měli byste odpovídajícím způsobem upravit kroky.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1: Zakázání plánovače synchronizace a ověření, že neprobíhá žádná synchronizace
Chcete-li zabránit nechtěným změnám exportovaných do služby Azure AD, ujistěte se, že při aktualizaci pravidel synchronizace neprobíhá žádná synchronizace. Zakázání integrovaného plánovače synchronizace:

1. Spusťte relaci Prostředí PowerShell na serveru Azure AD Connect.
2. Zakázat plánovanou synchronizaci spuštěním `Set-ADSyncScheduler -SyncCycleEnabled $false`této rutiny: .
3. Spusťte **Správce synchronizačních služeb** na spouštění**služby** **START** > .
4. Vyberte kartu **Operace** a zkontrolujte, zda neprobíhá žádná operace se stavem *probíhá*.

![Snímek obrazovky správce synchronizačních služeb](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>Krok 2: Aktualizace schématu pro službu Active Directory
Pokud jste aktualizovali schéma služby Active Directory na 2019 a připojení bylo nainstalováno před rozšířením schématu, pak mezipaměť schématu Připojení nemá aktualizované schéma. Potom je nutné aktualizovat schéma z průvodce, aby se zobrazilo v unovém rozhraní.

1. Spusťte průvodce Azure AD Connect z plochy.
2. Vyberte možnost **Aktualizovat schéma adresáře** a klepněte na tlačítko **Další**.
3. Zadejte přihlašovací údaje azure ad a klikněte na **další**.
4. Na stránce **Aktualizovat schéma adresáře** zkontrolujte, zda jsou vybrány všechny doménové struktury, a klepněte na tlačítko **Další**.
5. Po dokončení zavřete průvodce.

![Snímek obrazovky se schématem aktualizovat adresář v průvodci Připojit](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Krok 3: Přidání atributu source do místního schématu konektoru služby Active Directory
**Tento krok je potřeba pouze v případě, že spustíte připojení verze 1.3.21 nebo starší. Pokud jste na 1.4.18 nebo novější, přejděte ke kroku 5.**  
Ne všechny atributy Azure AD se importují do místního prostoru konektoru služby Active Directory. Pokud jste zvolili použití atributu, který není ve výchozím nastavení synchronizován, je třeba jej importovat. Přidání atributu zdroj do seznamu importovaných atributů:

1. Vyberte kartu **Konektory** ve Správci synchronizačních služeb.
2. Klepněte pravým tlačítkem myši na místní konektor služby Active Directory a vyberte příkaz **Vlastnosti**.
3. V rozbalovacím dialogovém okně přejděte na kartu **Vybrat atributy.**
4. Ujistěte se, že zdrojový atribut, který jste vybrali, je zaškrtnut v seznamu atributů. Pokud svůj atribut nevidíte, zaškrtněte políčko **Zobrazit vše.**
5. Chcete-li soubor uložit, vyberte **možnost OK**.

![Snímek obrazovky s dialogovým oknem Správce služeb synchronizace a vlastnosti](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Krok 4: Přidání **upřednostňovaného umístění DataLok** do schématu konektoru Azure AD Connector
**Tento krok je potřeba pouze v případě, že spustíte připojení verze 1.3.21 nebo starší. Pokud jste na 1.4.18 nebo novější, přejděte ke kroku 5.**  
Ve výchozím nastavení **preferredDataLocation** atribut není importován do prostoru konektoru Azure AD. Chcete-li jej přidat do seznamu importovaných atributů:

1. Vyberte kartu **Konektory** ve Správci synchronizačních služeb.
2. Klikněte pravým tlačítkem myši na konektor Azure AD a vyberte **vlastnosti**.
3. V rozbalovacím dialogovém okně přejděte na kartu **Vybrat atributy.**
4. V seznamu vyberte atribut **preferredDataLocation.**
5. Chcete-li soubor uložit, vyberte **možnost OK**.

![Snímek obrazovky s dialogovým oknem Správce služeb synchronizace a vlastnosti](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>Krok 5: Vytvoření pravidla příchozí synchronizace
Pravidlo příchozí synchronizace umožňuje, aby hodnota atributu přetekla z atributu source v místním adresáři Active Directory do metaverse.

1. Spusťte **Editor pravidel synchronizace** tak, že **přejdete** > do**Editoru pravidel synchronizace**.
2. Nastavte vyhledávací filtr **Směr** na **Příchozí**.
3. Chcete-li vytvořit nové příchozí pravidlo, vyberte **Přidat nové pravidlo**.
4. Na kartě **Popis** zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | --- | --- | --- |
    | Name (Název) | *Zadejte název* | Například "In from AD – User preferredDataLocation" |
    | Popis | *Zadání vlastního popisu* |  |
    | Připojený systém | *Vyberte místní konektor služby Active Directory* |  |
    | Typ připojeného systémového objektu | **Uživatel** |  |
    | Typ objektu Metaverse | **Person (Osoba)** |  |
    | Typ propojení | **Připojit** |  |
    | Priorita | *Zvolte číslo mezi 1–99* | 1–99 je vyhrazena pro vlastní pravidla synchronizace. Nevybírejte hodnotu, která je používána jiným pravidlem synchronizace. |

5. Ponechte **filtr oborů** prázdný, aby zahrnoval všechny objekty. Možná budete muset vyladit filtr oborů podle vašeho nasazení Azure AD Connect.
6. Přejděte na **kartu Transformace**a implementujte následující pravidlo transformace:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Typ sloučení |
    | --- | --- | --- | --- | --- |
    |Direct | preferredDataLocation | Vyberte zdrojový atribut | Nekontrolovaná | Aktualizace |

7. Chcete-li vytvořit příchozí pravidlo, vyberte **přidat**.

![Snímek obrazovky s pravidlem vytvoření příchozí synchronizace](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>Krok 6: Vytvoření pravidla odchozí synchronizace
Pravidlo odchozí synchronizace umožňuje, aby hodnota atributu proudila z metaverse do atributu **preferredDataLocation** ve službě Azure AD:

1. Přejděte do **Editoru pravidel synchronizace**.
2. Nastavte vyhledávací filtr **Směr** jako **odchozí**.
3. Vyberte **Přidat nové pravidlo**.
4. Na kartě **Popis** zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | ----- | ------ | --- |
    | Name (Název) | *Zadejte název* | Například "Out to Azure AD – User preferredDataLocation" |
    | Popis | *Uveďte popis* ||
    | Připojený systém | *Výběr konektoru Azure AD* ||
    | Typ připojeného systémového objektu | **Uživatel** ||
    | Typ objektu Metaverse | **Person (Osoba)** ||
    | Typ propojení | **Připojit** ||
    | Priorita | *Zvolte číslo mezi 1–99* | 1–99 je vyhrazena pro vlastní pravidla synchronizace. Nevybírejte hodnotu, která je používána jiným pravidlem synchronizace. |

5. Přejděte na kartu **filtru oborů** a přidejte jednu skupinu filtrů oboru se dvěma klauzulemi:

    | Atribut | Operátor | Hodnota |
    | --- | --- | --- |
    | sourceObjectType | Stejné | Uživatel |
    | cloudMastered | Notequal | True |

    Oborový filtr určuje, které objekty Azure AD toto pravidlo odchozí synchronizace se použije. V tomto příkladu používáme stejný filtr oborů z pravidla synchronizace "Out to Azure AD – Identita uživatele" OOB (out-of-box). Zabraňuje použití pravidla synchronizace na **objekty uživatele,** které nejsou synchronizovány z místní služby Active Directory. Možná budete muset vyladit filtr oborů podle vašeho nasazení Azure AD Connect.

6. Přejděte na kartu **Transformace** a implementujte následující pravidlo transformace:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Typ sloučení |
    | --- | --- | --- | --- | --- |
    | Direct | preferredDataLocation | preferredDataLocation | Nekontrolovaná | Aktualizace |

7. Zavřete **Přidat** a vytvořte odchozí pravidlo.

![Snímek obrazovky s pravidlem vytvořit odchozí synchronizaci](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>Krok 7: Spuštění úplného cyklu synchronizace
Obecně je vyžadován úplný cyklus synchronizace. Důvodem je, že jste přidali nové atributy do schématu služby Active Directory a služby Azure AD Connector a zavedli vlastní pravidla synchronizace. Před exportem do Azure AD ověřte změny. Můžete použít následující kroky k ověření změn, zatímco ručně spuštění kroků, které tvoří úplný cyklus synchronizace.

1. Spusťte **úplný import** v místním konektoru služby Active Directory:

   1. Přejděte na kartu **Operace** ve Správci synchronizačních služeb.
   2. Klepněte pravým tlačítkem myši **na místní konektor služby Active Directory**a vyberte příkaz **Spustit**.
   3. V dialogovém okně vyberte **Úplné importace**a vyberte **OK**.
   4. Počkejte na dokončení operace.

      > [!NOTE]
      > Úplný import v místním konektoru služby Active Directory můžete přeskočit, pokud je zdrojový atribut již zahrnut v seznamu importovaných atributů. Jinými slovy, nebylo třeba provést žádné změny během kroku 2 dříve v tomto článku.

2. Spusťte **úplný import** na konektoru Azure AD:

   1. Klikněte pravým tlačítkem myši na **konektor Azure AD a**vyberte **spustit**.
   2. V dialogovém okně vyberte **Úplné importace**a vyberte **OK**.
   3. Počkejte na dokončení operace.

3. Ověřte změny pravidel synchronizace u existujícího objektu **User.**

   Zdrojový atribut z místní služby Active Directory a **preferredDataLocation** z Azure AD byly importovány do každého příslušného prostoru konektoru. Než budete pokračovat v úplném kroku synchronizace, proveďte náhled existujícího objektu **User** v místním prostoru programu Active Directory Connector. Vybraný objekt by měl mít vyplněný zdrojový atribut. Úspěšný náhled s **preferredDataLocation** naplněný v metaverse je dobrým ukazatelem, že jste správně nakonfigurovali pravidla synchronizace. Informace o tom, jak provést náhled, naleznete [v tématu Ověření změny](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Spusťte **úplnou synchronizaci** v místním konektoru služby Active Directory:

   1. Klepněte pravým tlačítkem myši **na místní konektor služby Active Directory**a vyberte příkaz **Spustit**.
   2. V dialogovém okně vyberte **Úplnou synchronizaci**a vyberte **OK**.
   3. Počkejte na dokončení operace.

5. **Ověřte čekající exporty** do Azure AD:

   1. Klikněte pravým tlačítkem myši na **konektor Azure AD a**vyberte možnost Hledat místo v **konektoru**.
   2. V dialogovém okně **Prostor pro hledání:**

        a. Nastavte **obor** na **čekající export**.<br>
        b. Zaškrtněte všechna tři zaškrtávací políčka, včetně **přidat, upravit a odstranit**.<br>
        c. Chcete-li zobrazit seznam objektů se změnami, které mají být exportovány, vyberte **možnost Hledat**. Chcete-li zkontrolovat změny pro daný objekt, poklepejte na objekt.<br>
        d. Ověřte, zda jsou očekávané změny.

6. Spuštění **exportu** v **konektoru Azure AD**

   1. Klikněte pravým tlačítkem myši na **konektor Azure AD a**vyberte **spustit**.
   2. V dialogovém okně **Spustit spojnici** vyberte **Exportovat**a vyberte **OK**.
   3. Počkejte na dokončení operace.

> [!NOTE]
> Můžete si všimnout, že kroky nezahrnují úplný krok synchronizace na konektoru Azure AD nebo krok exportu na konektoru služby Active Directory. Kroky nejsou povinné, protože hodnoty atributů jsou tok z místní služby Active Directory pouze do služby Azure AD.

## <a name="step-8-re-enable-sync-scheduler"></a>Krok 8: Znovu povolit plánovač synchronizace
Znovu povolte integrovaný plánovač synchronizace:

1. Spusťte relaci Prostředí PowerShell.
2. Znovu povolte naplánovanou synchronizaci spuštěním této rutiny:`Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-9-verify-the-result"></a>Krok 9: Ověření výsledku
Nyní je čas ověřit konfiguraci a povolit ji pro uživatele.

1. Přidejte geo do vybraného atributu uživatele. Seznam dostupných geos naleznete v této tabulce.  
![Snímek obrazovky s atributem AD přidaným uživateli](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Počkejte na atribut, který má být synchronizován do Služby Azure AD.
3. Pomocí prostředí Exchange Online PowerShell ověřte, zda byla oblast poštovní schránky správně nastavena.  
![Snímek obrazovky s Prostředím Exchange Online PowerShell](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Za předpokladu, že váš tenant byl označen, aby bylo možné používat tuto funkci, poštovní schránka se přesune na správnou geografickou. To lze ověřit při pohledu na název serveru, kde je umístěnpoštovní schránka.

## <a name="next-steps"></a>Další kroky

Další informace o multigeo v Office 365:

* [Multi-Geo sessions ve společnosti Ignite](https://aka.ms/MultiGeoIgnite)
* [Multi-Geo na OneDrivu](https://aka.ms/OneDriveMultiGeo)
* [Multi-Geo v SharePointu Online](https://aka.ms/SharePointMultiGeo)

Další informace o modelu konfigurace v synchronizačním modulu:

* Přečtěte si další informace o modelu konfigurace v [principu deklarativní zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Přečtěte si další informace o jazyku výrazu v [principu deklarativní zřizování výrazy](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Přehledná témata:

* [Synchronizace služby Azure AD Connect: Principy a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

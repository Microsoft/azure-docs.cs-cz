---
title: 'Azure AD Connect: konfigurace upřednostňovaného umístění dat pro prostředky Microsoft 365'
description: V této části najdete popis postupu přepnutí Microsoft 365ch uživatelských prostředků do uživatele s Azure Active Directory Connect synchronizace.
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
ms.topic: how-to
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c456e7788280b7ca5328342e1cd848ba3a583a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95972755"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-microsoft-365-resources"></a>Azure Active Directory Connect synchronizace: konfigurace upřednostňovaného umístění dat pro prostředky Microsoft 365
Účelem tohoto tématu je projít si, jak nakonfigurovat atribut pro preferované umístění dat v Azure Active Directory (Azure AD) Connect Sync. Pokud někdo používá v Microsoft 365 více geografických možností, použijte tento atribut k určení geografického umístění dat Microsoft 365 uživatele. ( *Oblast* podmínek a *geografické* použití jsou zaměnitelné.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Povolit synchronizaci umístění upřednostňovaného data
Ve výchozím nastavení se Microsoft 365 prostředky pro uživatele nacházejí ve stejné geografické podobě jako váš tenant Azure AD. Pokud se například váš tenant nachází v Severní Amerika, jsou poštovní schránky Exchange uživatele také umístěny v Severní Amerika. Pro nadnárodní organizaci to nemusí být optimální.

Nastavením atributu **preferredDataLocation** můžete definovat geografickou hodnotu uživatele. Můžete mít Microsoft 365 prostředky, jako je poštovní schránka a OneDrive, ve stejné geografické podobě jako uživatel a mít pro celou organizaci i jednoho tenanta.

> [!IMPORTANT]
> Služba multi-Geo je aktuálně dostupná pro zákazníky s aktivním smlouva Enterprise a minimálními předplatnými 250 Microsoft 365 Services. Podrobnosti získáte od zástupce Microsoftu.
>
>

Seznam všech zeměpisných oblastech pro Microsoft 365 najdete v části [kde se nacházejí vaše data?](/microsoft-365/enterprise/o365-data-locations).

Zeměpisných oblastech v Microsoft 365 k dispozici pro více geografických umístění:

| Geografická oblast | hodnota preferredDataLocation |
| --- | --- |
| Asie a Tichomoří | KONTAKTOVAT |
| Austrálie | STŘEDNÍ |
| Kanada | NEMŮŽE |
| Evropská unie | EUR |
| Francie | FRA |
| Indie | IND |
| Japonsko | JPN |
| Jižní Korea | KOR |
| Jižní Afrika | ZAF |
| Švýcarsko | CYRILICE |
| Spojené arabské emiráty | JSOU |
| Spojené království | GBR |
| USA | NAM |

* Pokud geografická oblast není uvedená v této tabulce (například Jižní Amerika), nedá se použít pro více geografických oblastí.

* Ne všechny Microsoft 365 úlohy podporují použití nastavení geografického uživatele.

### <a name="azure-ad-connect-support-for-synchronization"></a>Azure AD Connect podpora synchronizace

Azure AD Connect podporuje synchronizaci atributu **preferredDataLocation** pro **uživatelské** objekty ve verzi 1.1.524.0 a novější. Konkrétně se jedná o tyto:

* Schéma typu objektu **uživatele** v konektoru služby Azure AD je rozšířené tak, aby zahrnovalo atribut **preferredDataLocation** . Atribut je typu, což je řetězec s jednou hodnotou.
* Schéma objektu typu **osoba** v úložišti metaverse je rozšířeno tak, aby zahrnovalo atribut **preferredDataLocation** . Atribut je typu, což je řetězec s jednou hodnotou.

Ve výchozím nastavení není **preferredDataLocation** povolená synchronizace. Tato funkce je určená pro větší organizace. Schéma služby Active Directory ve Windows serveru 2019 má atribut **msDS-preferredDataLocation** , který byste měli použít k tomuto účelu. Pokud jste schéma služby Active Directory neaktualizovali a nemůžete tak učinit, je nutné určit atribut, který bude pro uživatele uchovávat Microsoft 365 geograficky. To se pro každou organizaci liší.

> [!IMPORTANT]
> Azure AD umožňuje, aby byl atribut **preferredDataLocation** u **uživatelských objektů cloudového uživatele** přímo nakonfigurovaný pomocí Azure AD PowerShellu. Chcete-li nakonfigurovat tento atribut u **synchronizovaných uživatelských objektů**, je nutné použít Azure AD Connect.

Než povolíte synchronizaci:

* Pokud jste schéma služby Active Directory neupgradovali na 2019, pak se rozhodněte, který místní atribut služby Active Directory se má použít jako zdrojový atribut. Měl by být typu **řetězec s jednou hodnotou**.
* Pokud jste dříve nakonfigurovali atribut **preferredDataLocation** u stávajících **synchronizovaných uživatelských objektů** ve službě Azure AD pomocí Azure AD PowerShellu, musíte Backport hodnoty atributu k odpovídajícím objektům **uživatele** v místní službě Active Directory.

    > [!IMPORTANT]
    > Pokud tyto hodnoty nebackportte, Azure AD Connect odstraní existující hodnoty atributu v Azure AD, když je povolená synchronizace pro atribut **preferredDataLocation** .

* Nakonfigurujte zdrojový atribut alespoň na několika místních uživatelských objektech služby Active Directory. Tuto možnost můžete použít pro ověření později.

Následující části obsahují postup pro povolení synchronizace atributu **preferredDataLocation** .

> [!NOTE]
> Tento postup je popsaný v kontextu nasazení Azure AD s topologií s jednou doménovou strukturou a bez vlastních synchronizačních pravidel. Pokud máte topologii s více doménovými strukturami, nakonfigurovali jste vlastní pravidla synchronizace nebo máte pracovní server, měli byste tyto kroky upravit odpovídajícím způsobem.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1: zakázání plánovače synchronizace a ověření, že probíhá žádná synchronizace
Aby se zabránilo tomu, že se nezamýšlené změny exportují do služby Azure AD, zajistěte, aby žádná synchronizace neprobíhala v průběhu aktualizace synchronizačních pravidel. Zakázání integrovaného plánovače synchronizace:

1. Spusťte relaci PowerShellu na Azure AD Connectovém serveru.
2. Zakažte naplánovanou synchronizaci spuštěním této rutiny: `Set-ADSyncScheduler -SyncCycleEnabled $false` .
3. Spusťte **Synchronization Service Manager** **spuštěním**  >  **synchronizační služby**.
4. Vyberte kartu **operace** a potvrďte, že neexistuje žádná operace s *probíhajícím* stavem.

![Snímek obrazovky s Synchronization Service Manager](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>Krok 2: aktualizace schématu pro službu Active Directory
Pokud jste aktualizovali schéma služby Active Directory na 2019 a připojili jste instalaci ještě před rozšířením schématu, pak mezipaměť připojit schématu nemá aktualizované schéma. Pak je třeba aktualizovat schéma v průvodci, aby se zobrazilo v uživatelském rozhraní.

1. Spusťte Průvodce Azure AD Connect z plochy.
2. Vyberte možnost **Aktualizovat schéma adresáře** a klikněte na **Další**.
3. Zadejte svoje přihlašovací údaje služby Azure AD a klikněte na **Další**.
4. Na stránce **Aktualizovat schéma adresáře** se ujistěte, že jsou vybrané všechny doménové struktury, a klikněte na **Další**.
5. Po dokončení zavřete průvodce.

![Snímek obrazovky se schématem aktualizace adresáře v Průvodci připojením](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Krok 3: Přidání zdrojového atributu do místního schématu konektoru služby Active Directory
**Tento krok je nutný jenom v případě, že používáte Connect verze 1.3.21 nebo starší. Pokud používáte 1.4.18 nebo novější, přejděte ke kroku 5.**  
Ne všechny atributy služby Azure AD se importují do místního prostoru konektoru služby Active Directory. Pokud jste vybrali použití atributu, který není ve výchozím nastavení synchronizovaný, musíte ho importovat. Chcete-li přidat zdrojový atribut do seznamu importovaných atributů:

1. Vyberte kartu **konektory** v Synchronization Service Manager.
2. Klikněte pravým tlačítkem na místní konektor služby Active Directory a vyberte **vlastnosti**.
3. V místním dialogovém okně přejdete na kartu **vybrat atributy** .
4. Ujistěte se, že zdrojový atribut, který jste vybrali k použití, je zaškrtnuté v seznamu atributů. Pokud atribut nevidíte, zaškrtněte políčko **Zobrazit vše** .
5. Pokud ho chcete uložit, vyberte **OK**.

![Snímek obrazovky, který zobrazuje dialogové okno Synchronization Service Manager a vlastnosti se zvýrazněným seznamem atributy.](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Krok 4: přidání **preferredDataLocation** do schématu konektoru služby Azure AD
**Tento krok je nutný jenom v případě, že používáte Connect verze 1.3.21 nebo starší. Pokud používáte 1.4.18 nebo novější, přejděte ke kroku 5.**  
Ve výchozím nastavení se neimportuje atribut **preferredDataLocation** do prostoru konektoru služby Azure AD. Pro přidání do seznamu importovaných atributů:

1. Vyberte kartu **konektory** v Synchronization Service Manager.
2. Klikněte pravým tlačítkem na konektor Azure AD a vyberte **vlastnosti**.
3. V místním dialogovém okně přejdete na kartu **vybrat atributy** .
4. V seznamu vyberte atribut **preferredDataLocation** .
5. Pokud ho chcete uložit, vyberte **OK**.

![Snímek obrazovky dialogového okna Synchronization Service Manager a vlastnosti](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>Krok 5: vytvoření pravidla pro příchozí synchronizaci
Pravidlo příchozí synchronizace povoluje, aby hodnota atributu mohla přecházet ze zdrojového atributu v místní službě Active Directory do úložiště metaverse.

1. Spusťte **Editor pravidel synchronizace** kliknutím na **Spustit**  >  **Editor pravidel synchronizace**.
2. Nastavte **směr** vyhledávacího filtru na **příchozí**.
3. Pokud chcete vytvořit nové příchozí pravidlo, vyberte **Přidat nové pravidlo**.
4. Na kartě **Popis** zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | --- | --- | --- |
    | Name | *Zadat název* | Například "in from AD – User preferredDataLocation" |
    | Description | *Zadejte vlastní popis.* |  |
    | Připojený systém | *Výběr místního konektoru služby Active Directory* |  |
    | Typ připojeného systémového objektu | **Uživatel** |  |
    | Typ objektu úložiště metaverse | **Person (Osoba)** |  |
    | Typ odkazu | **Join** (Spojení) |  |
    | Priorita | *Vyberte číslo v rozmezí 1 až 99.* | 1 – 99 je vyhrazeno pro vlastní pravidla synchronizace. Nevybírejte hodnotu, kterou používá jiné synchronizační pravidlo. |

5. Pokud chcete zahrnout všechny objekty, nechejte **Filtr oboru** prázdný. Je možné, že budete muset upravit filtr oboru podle nasazení Azure AD Connect.
6. Přejít na **kartu transformace** a implementovat následující pravidlo transformace:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Typ sloučení |
    | --- | --- | --- | --- | --- |
    |Direct | preferredDataLocation | Vyberte zdrojový atribut | Nezaškrtnuto | Aktualizace |

7. Pokud chcete vytvořit příchozí pravidlo, vyberte **Přidat**.

![Snímek obrazovky s pravidly vytvoření příchozí synchronizace](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>Krok 6: vytvoření pravidla odchozí synchronizace
Pravidlo odchozí synchronizace povoluje, aby hodnota atributu byla z úložiště metaverse předávána do atributu **preferredDataLocation** v Azure AD:

1. Umožňuje přejít do **editoru pravidel synchronizace**.
2. Nastavte **směr** vyhledávacího filtru na **odchozí**.
3. Vyberte **Přidat nové pravidlo**.
4. Na kartě **Popis** zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | ----- | ------ | --- |
    | Name | *Zadat název* | Například "odchozí do Azure AD – User preferredDataLocation" |
    | Description | *Zadejte popis.* ||
    | Připojený systém | *Vyberte konektor Azure AD.* ||
    | Typ připojeného systémového objektu | **Uživatel** ||
    | Typ objektu úložiště metaverse | **Person (Osoba)** ||
    | Typ odkazu | **Join** (Spojení) ||
    | Priorita | *Vyberte číslo v rozmezí 1 až 99.* | 1 – 99 je vyhrazeno pro vlastní pravidla synchronizace. Nevybírejte hodnotu, kterou používá jiné synchronizační pravidlo. |

5. Přejít na kartu **obor filtru** a přidejte jednu skupinu filtru oborů se dvěma klauzulemi:

    | Atribut | Operátor | Hodnota |
    | --- | --- | --- |
    | sourceObjectType | VÝŠI | User |
    | cloudMastered | NOTEQUAL | Ano |

    Filtr oboru určuje, na které objekty služby Azure AD se toto pravidlo odchozí synchronizace použije. V tomto příkladu používáme stejný filtr pro vytváření oborů z "out do Azure AD – identita uživatele" OOB (dopředný) – pravidlo synchronizace. Zabraňuje použití synchronizačního pravidla pro **uživatelské** objekty, které nejsou synchronizované z místní služby Active Directory. Je možné, že budete muset upravit filtr oboru podle nasazení Azure AD Connect.

6. Přejít na kartu **transformace** a implementovat následující pravidlo transformace:

    | Typ toku | Cílový atribut | Zdroj | Použít jednou | Typ sloučení |
    | --- | --- | --- | --- | --- |
    | Direct | preferredDataLocation | preferredDataLocation | Nezaškrtnuto | Aktualizace |

7. Pokud chcete vytvořit odchozí pravidlo, zavřete **Přidat** .

![Snímek obrazovky pro vytvoření pravidla odchozí synchronizace](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>Krok 7: spuštění úplného cyklu synchronizace
Obecně je potřeba úplný cyklus synchronizace. Důvodem je to, že jste přidali nové atributy do schématu služby Active Directory a konektoru služby Azure AD a zavedli jste vlastní pravidla synchronizace. Před exportem do služby Azure AD ověřte změny. Pomocí následujících kroků můžete ověřit změny a ručně spustit kroky, které tvoří cyklus úplné synchronizace.

1. Spustit **úplný import** na místní konektor služby Active Directory:

   1. V Synchronization Service Manager otevřete kartu **operace** .
   2. Klikněte pravým tlačítkem na **místní konektor služby Active Directory** a vyberte **Spustit**.
   3. V dialogovém okně vyberte **úplný import** a vyberte **OK**.
   4. Počkejte na dokončení operace.

      > [!NOTE]
      > Pokud je zdrojový atribut již obsažen v seznamu importovaných atributů, můžete přeskočit úplný import na místní konektor služby Active Directory. Jinými slovy, nemusíte provádět žádné změny během kroku 2 výše v tomto článku.

2. Spusťte **úplný import** na konektoru služby Azure AD:

   1. Klikněte pravým tlačítkem na **konektor Azure AD** a vyberte **Spustit**.
   2. V dialogovém okně vyberte **úplný import** a vyberte **OK**.
   3. Počkejte na dokončení operace.

3. Ověřte změny synchronizačního pravidla u stávajícího objektu **uživatele** .

   Zdrojový atribut z místní služby Active Directory a **preferredDataLocation** z Azure AD se importoval do každého příslušného prostoru konektoru. Než budete pokračovat v kroku úplné synchronizace, udělejte ve verzi Preview existující objekt **uživatele** v prostoru konektoru služby Active Directory. Objekt, který jste vybrali, by měl mít naplněný zdrojový atribut. Úspěšná verze Preview s **preferredDataLocation** naplněná v úložišti metaverse je dobrým indikátorem, že jste správně nakonfigurovali pravidla synchronizace. Informace o tom, jak si prohlédnout verzi Preview, najdete v tématu [ověření změny](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Spusťte **úplnou synchronizaci** na místním konektoru služby Active Directory:

   1. Klikněte pravým tlačítkem na **místní konektor služby Active Directory** a vyberte **Spustit**.
   2. V dialogovém okně vyberte možnost **Úplná synchronizace** a pak vyberte **OK**.
   3. Počkejte na dokončení operace.

5. Ověřit **nedokončené exporty** do Azure AD:

   1. Klikněte pravým tlačítkem na **konektor Azure AD** a vyberte **Hledat místo v konektoru**.
   2. V dialogovém okně **Hledat místo konektoru** :

        a. Nastavte **Rozsah** na **Export čeká na vyřízení**.<br>
        b. Zaškrtněte všechna tři zaškrtávací políčka, včetně **Přidat, upravit a odstranit**.<br>
        c. Chcete-li zobrazit seznam objektů se změnami, které mají být exportovány, vyberte **Hledat**. Chcete-li prostudovat změny pro daný objekt, dvakrát klikněte na objekt.<br>
        d. Ověřte, zda jsou tyto změny očekávány.

6. Spuštění **exportu** v **konektoru služby Azure AD**

   1. Klikněte pravým tlačítkem na **konektor Azure AD** a vyberte **Spustit**.
   2. V dialogovém okně **Spustit konektor** vyberte **exportovat** a vyberte **OK**.
   3. Počkejte na dokončení operace.

> [!NOTE]
> Můžete si všimnout, že kroky nezahrnují krok úplné synchronizace na konektoru služby Azure AD nebo krok exportu v konektoru služby Active Directory. Kroky nejsou vyžadovány, protože hodnoty atributu jsou z místní služby Active Directory předávány pouze do služby Azure AD.

## <a name="step-8-re-enable-sync-scheduler"></a>Krok 8: opětovné povolení plánovače synchronizace
Opětovné povolení integrovaného plánovače synchronizace:

1. Spusťte relaci PowerShellu.
2. Spusťte tuto rutinu znovu, aby se naplánovala synchronizace: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-9-verify-the-result"></a>Krok 9: ověření výsledku
Nyní je čas ověřit konfiguraci a povolit pro uživatele.

1. Přidejte geografické rozhraní k vybranému atributu pro uživatele. Seznam dostupných zeměpisných oblastech najdete v této tabulce.  
![Snímek obrazovky s atributem AD přidaným uživateli](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Počkejte, až se atribut synchronizuje do Azure AD.
3. Pomocí Exchange Online PowerShellu ověřte, že je správně nastavená oblast poštovní schránky.  
![Snímek obrazovky s Exchangem Online PowerShellem](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Za předpokladu, že je váš tenant označený k tomu, aby mohl používat tuto funkci, se poštovní schránka přesune do správného geografického umístění. Můžete to ověřit tak, že si vyhledáte název serveru, kde se nachází poštovní schránka.

## <a name="next-steps"></a>Další kroky

Další informace o více geografických umístěních v Microsoft 365:

* [Více geografických relací na Ignite](https://aka.ms/MultiGeoIgnite)
* [Více geografických umístění na OneDrivu](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)
* [Více geografických umístění v SharePointu Online](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)

Další informace o modelu konfigurace v synchronizačním modulu:

* Přečtěte si další informace o modelu konfigurace v tématu [Principy deklarativního zřizování](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Přečtěte si další informace o jazyce výrazů v tématu [Principy deklarativních zřizovacích výrazů](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Témata přehledu:

* [Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
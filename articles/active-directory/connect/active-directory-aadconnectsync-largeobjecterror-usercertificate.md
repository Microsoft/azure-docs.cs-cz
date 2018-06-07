---
title: Azure AD Connect – LargeObject chyby způsobené userCertificate atribut | Microsoft Docs
description: Toto téma obsahuje postup nápravy LargeObject chyby způsobené userCertificate atribut.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 9866454735b33239a812dca238006299c74e5ae2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592802"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Synchronizace Azure AD Connect: LargeObject zpracování chyb vzniklých při userCertificate atribut

Azure AD vynucuje maximální limit **15** certifikátů hodnoty na **userCertificate** atribut. Pokud Azure AD Connect exportuje objekt s více než 15 hodnoty do služby Azure AD, Azure AD vrátí **LargeObject** chybová zpráva:

>*"Zřízený objekt je příliš velký. Omezte počet hodnot atributů pro tento objekt. Operace bude opakována při příštím synchronizačním cyklu..."*

Ostatní atributy AD může být způsobeno LargeObject chyba. Pokud chcete potvrdit, je skutečně způsobená userCertificate atribut, je třeba ověřit pro objekt buď v místní službě AD, nebo v [hledání úložiště Metaverse Synchronization Service Manager](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch).

Pokud chcete získat seznam objektů ve vašem klientovi s chybami LargeObject, použijte jednu z následujících metod:

 * Pokud je váš klient Azure AD Connect Health pro synchronizaci, můžete se podívat do [synchronizace chybách](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync#object-level-synchronization-error-report-preview) zadat.
 
 * Oznámení e-mailu pro chyby synchronizace adresáře odeslaný na konci každé synchronizačním cyklu má seznam objektů s chybami LargeObject. 
 * [Kartu Synchronization Service Manager operace](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) zobrazí seznam objektů s chybami LargeObject, pokud kliknete na poslední Export do Azure AD operaci.
 
## <a name="mitigation-options"></a>Zmírnění dopadů možnosti
Až do vyřešení chyby LargeObject jiné změny atributů ke stejnému objektu nelze exportovat do služby Azure AD. Opravte případné chyby, zvažte následující možnosti:

 * Upgrade Azure AD Connect k sestavení 1.1.524.0 nebo po. Ve službě Azure AD Connect sestavení 1.1.524.0, out-of-box synchronizace, které byly aktualizovány pravidla není export userCertificate atributy a userSMIMECertificate, pokud máte více než 15 hodnoty atributů. Podrobnosti o tom, jak upgradovat Azure AD Connect, najdete v článku [Azure AD Connect: Upgrade z předchozí verze na nejnovější](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

 * Implementace **pravidlo odchozí synchronizace** v Azure AD Connect, který exportuje **hodnota místo skutečnými hodnotami pro objekty s více než 15 certifikát hodnoty null**. Tato možnost je vhodná, pokud nechcete, aby všechny hodnoty certifikát tak, aby exportovány do služby Azure AD pro objekty s více než 15 hodnotami. Podrobnosti o tom, jak implementovat toto synchronizační pravidlo, najdete další části [implementace synchronizační pravidlo omezit export userCertificate atributu](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Snižte počet hodnot certifikát na místní objektu AD (maximálně 15) odebráním hodnoty, které jsou již používá vaše organizace. To je vhodné, pokud je atribut tomu způsobeno certifikáty s vypršenou platností nebo se nepoužívá. Můžete použít [zde k dispozici skript prostředí PowerShell](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) vám pomohou najít, zálohování a odstranit certifikáty s vypršenou platností v místní AD. Před odstraněním certifikáty, se doporučuje, abyste ověřili se infrastruktura veřejných klíčů správci ve vaší organizaci.

 * Konfigurujte Azure AD Connect vyloučit atribut userCertificate z se exportují do služby Azure AD. Obecně nedoporučujeme tuto možnost vzhledem k tomu, že atribut může být používán služeb Microsoft Online Services, které umožňují konkrétní scénáře. Konkrétně:
    * Atribut userCertificate v objektu User je používán Exchange Online a klienty Outlook pro zprávu podepisování a šifrování. Další informace o této funkci naleznete v článku [S/MIME pro zprávu podepisování a šifrování](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx).

    * Atribut userCertificate na objekt počítače se používá Azure AD umožňuje Windows 10 v místní doméně zařízení pro připojení k Azure AD. Další informace o této funkci naleznete v článku [připojení zařízení připojených k doméně ke službě Azure AD pro Windows 10 vyskytne](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementace synchronizační pravidlo omezit export userCertificate atributu
Chcete-li vyřešit chyby LargeObject způsobené userCertificate atribut, můžete implementovat pravidlo odchozí synchronizace v Azure AD Connect, který exportuje **hodnota místo skutečných hodnot pro objekty s více než 15 certifikát hodnotynull**. Tato část popisuje kroky nutné k implementaci synchronizační pravidlo pro **uživatele** objekty. Postup může být přizpůsobena pro **kontaktujte** a **počítače** objekty.

> [!IMPORTANT]
> Export hodnotu null odebere certifikát hodnoty dříve úspěšně exportovány do služby Azure AD.

Kroky můžete shrnuto jako:

1. Zakažte plánovače synchronizace a ověřte, že neexistuje žádná synchronizace v průběhu.
3. Pro atribut userCertificate najít existující pravidlo odchozí synchronizace.
4. Vytvořte pravidlo odchozí synchronizace vyžaduje.
5. Ověřte nové pravidlo synchronizace na existující objekt s chybou LargeObject.
6. Nové pravidlo synchronizace použito na zbývající objekty s chybou LargeObject.
7. Ověřte neexistují žádné neočekávané změny čekající na exportovány do služby Azure AD.
8. Exportujte změny do Azure AD.
9. Plánování pro synchronizační nástroj znovu povolte.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1. Zakažte plánovače synchronizace a ověřte, že neexistuje žádná synchronizace v průběhu
Zajistěte, aby že žádná synchronizace bude probíhat, když jste uprostřed implementace nové pravidlo synchronizace, aby se zabránilo neúmyslnému změny se exportují do služby Azure AD. Postup při zakázání plánovače předdefinované synchronizace:
1. Spusťte relaci prostředí PowerShell na server Azure AD Connect.

2. Zakážete plánované synchronizace spuštěním rutiny: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Předchozí kroky platí pouze pro novější verze (1.1.xxx.x) služby Azure AD Connect s integrovanou plánovače. Pokud používáte starší verze (1.0.xxx.x) služby Azure AD Connect, který používá plánovače úloh systému Windows nebo používáte vlastní vlastní plánovače (ne běžné) k aktivaci periodickou synchronizaci, musíte je zakázat odpovídajícím způsobem.

3. Spuštění **Synchronization Service Manager** přechodem na spuštění → synchronizační služba.

4. Přejděte na **operace** kartě a potvrďte neprobíhá žádná operace, jejichž stav je *"v průběhu."*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Krok 2. Pro atribut userCertificate najít existující pravidlo odchozí synchronizace
Měla by existovat existující synchronizační pravidlo, které je povolené a nakonfigurované pro export userCertificate atribut pro uživatelské objekty do služby Azure AD. Vyhledejte toto synchronizační pravidlo a zjistěte jeho **přednost** a **oboru filtru** konfigurace:

1. Spuštění **editoru pravidel synchronizace** tak, že přejdete do editoru pravidla synchronizace. počáteční →.

2. Filtry hledání konfigurace s následujícími hodnotami:

    | Atribut | Hodnota |
    | --- | --- |
    | Směr |**Odchozí** |
    | Typ objektu MV |**Osoba** |
    | Spojovací čára |*název vašeho konektoru služby Azure AD* |
    | Typ objektu konektoru |**Uživatel** |
    | Atribut MV |**userCertificate** |

3. Pokud používáte OOB (out-of-box) synchronizační pravidla pro Azure AD connector. Chcete-li exportovat userCertficiate atribut pro uživatelské objekty, měli byste obdržet *"Se do AAD – uživatel ExchangeOnline"* pravidlo.
4. Poznamenejte si **přednost** hodnotu toto synchronizační pravidlo.
5. Vyberte synchronizační pravidlo a klikněte na tlačítko **upravit**.
6. V *"Upravit vyhrazené pravidlo potvrzení"* automaticky otevíraný dialog, klikněte na tlačítko **ne**. (Nemusíte si dělat starosti, jsme nebudete provést změnu toto synchronizační pravidlo).
7. V dialogovém okně upravit, vyberte **Scoping filtru** kartě.
8. Poznamenejte si nastavení oboru filtru. Pokud používáte OOB synchronizační pravidlo, musí přesně být **jednoho oboru filtru skupina obsahující dvě klauzule**, včetně:

    | Atribut | Operátor | Hodnota |
    | --- | --- | --- |
    | sourceObjectType | ROVNÁ | Uživatel |
    | cloudMastered | NOTEQUAL | True |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Krok 3. Vytvořit požadované pravidlo odchozí synchronizace
Nové pravidlo synchronizace musí mít stejnou **oboru filtru** a **vyšší prioritu** než existující synchronizační pravidlo. To zajišťuje, aby nové pravidlo synchronizace se vztahuje na stejnou sadu objektů jako existující pravidlo synchronizace a přepíše existující synchronizační pravidlo pro atribut userCertificate. Vytvoření pravidla synchronizace:
1. V editoru pravidel synchronizace, klikněte **přidat nové pravidlo** tlačítko.
2. V části **kartě Popis**, zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | --- | --- | --- |
    | Název | *Zadejte název* | Například *"Se aad – vlastní přepsání pro userCertificate"* |
    | Popis | *Zadejte popis* | Například *"Pokud userCertificate atribut má více než 15 hodnot, export hodnotu NULL."* |
    | Připojený systém | *Vyberte konektoru služby Azure AD* |
    | Typ objektu systému připojené | **Uživatel** | |
    | Typ objektu úložiště Metaverse | **Osoba** | |
    | Typ propojení | **Spojení** | |
    | Priorita | *Zvolili číslo mezi 1 a 99* | Číslo zvolený nesmí používat žádné existující pravidlo synchronizace a má nižší hodnotu (a tedy vyšší prioritu) než existující synchronizační pravidlo. |

3. Přejděte na **Scoping filtru** kartě a implementovat stejné oboru filtru používá existující synchronizační pravidlo.
4. Přeskočit **připojení pravidla** kartě.
5. Přejděte na **transformace** a přidejte nový transformaci pomocí následující konfigurace:

    | Atribut | Hodnota |
    | --- | --- |
    | Typ toku |**výraz** |
    | Cílový atribut |**userCertificate** |
    | Zdrojový atribut |*Použijte následující výraz*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Klikněte **přidat** tlačítko pro vytvoření pravidla synchronizace.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Krok 4. Ověřte nové pravidlo synchronizace na existující objekt s chybou LargeObject
Toto je ověřte, zda pravidlo synchronizace vytvořené funguje správně na existující objekt AD s chybou LargeObject před použitím na jiné objekty:
1. Přejděte na **Operations** kartě Synchronization Service Manager.
2. Vyberte poslední Export do Azure AD operaci a klikněte na některý z objektů s chybami LargeObject.
3.  Na obrazovce automaticky otevírané okno Vlastnosti objektu prostoru konektoru, klikněte na **Preview** tlačítko.
4. Na obrazovce automaticky otevírané okno náhledu vyberte **úplné synchronizace** a klikněte na tlačítko **potvrdit Preview**.
5. Zavřete obrazovce Preview a na obrazovce vlastnosti objektu prostoru konektoru.
6. Přejděte na **konektory** kartě Synchronization Service Manager.
7. Klikněte pravým tlačítkem na **Azure AD** konektoru a vyberte **spustit...**
8. V místní nabídce spustit konektor, vyberte **exportovat** krok a klikněte na tlačítko **OK**.
9. Počkejte, než pro Export do Azure AD se dokončí a potvrďte, že se nezobrazí žádná chyba další LargeObject na tento konkrétní objekt.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Krok 5. Použít nové pravidlo synchronizace zbývající objektů s chybou LargeObject
Po přidání synchronizační pravidlo, budete muset spustit krok úplná synchronizace v konektoru AD:
1. Přejděte na **konektory** kartě Synchronization Service Manager.
2. Klikněte pravým tlačítkem na **AD** konektoru a vyberte **spustit...**
3. V místní nabídce spustit konektor, vyberte **úplná synchronizace** krok a klikněte na tlačítko **OK**.
4. Počkejte, než k dokončení kroku úplnou synchronizaci.
5. Pokud máte více než jednu AD konektory, opakujte výše uvedené kroky pro zbývající konektory AD. Obvykle se vyžadují více konektorů, pokud máte více místních adresářů.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Krok 6. Ověřte, neexistují žádné neočekávané změny čekající na exportovány do služby Azure AD
1. Přejděte na **konektory** kartě Synchronization Service Manager.
2. Klikněte pravým tlačítkem na **Azure AD** konektoru a vyberte **hledání prostoru konektoru**.
3. V místní nabídce hledání prostoru konektoru:
    1. Nastavit obor **čekající na vyřízení Export**.
    2. Zkontrolujte všechny 3 zaškrtávací políčka, včetně **přidat**, **upravit** a **odstranit**.
    3. Klikněte na tlačítko **vyhledávání** tlačítko vracet všechny objekty s změny čekající na exportovány do služby Azure AD.
    4. Ověřte, že neexistují žádné neočekávané změny. Pro zjištění změny pro daný objekt, dvakrát klikněte na objekt.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Krok 7. Exportovat změny do Azure AD
Export změny do Azure AD:
1. Přejděte na **konektory** kartě Synchronization Service Manager.
2. Klikněte pravým tlačítkem na **Azure AD** konektoru a vyberte **spustit...**
4. V místní nabídce spustit konektor, vyberte **exportovat** krok a klikněte na tlačítko **OK**.
5. Počkejte, než pro Export do Azure AD se dokončí a potvrďte, že nejsou žádné další chyby LargeObject.

### <a name="step-8-re-enable-sync-scheduler"></a>Krok 8. Opětovné povolení plánovače synchronizace
Teď, když je problém vyřešen, znovu povolte plánovače předdefinované synchronizace:
1. Spusťte relaci prostředí PowerShell.
2. Plánované synchronizace znovu povolte spuštěním rutiny: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Předchozí kroky platí pouze pro novější verze (1.1.xxx.x) služby Azure AD Connect s integrovanou plánovače. Pokud používáte starší verze (1.0.xxx.x) služby Azure AD Connect, který používá plánovače úloh systému Windows nebo používáte vlastní vlastní plánovače (ne běžné) k aktivaci periodickou synchronizaci, musíte je zakázat odpovídajícím způsobem.

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).


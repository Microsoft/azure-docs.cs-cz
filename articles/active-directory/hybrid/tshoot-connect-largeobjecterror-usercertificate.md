---
title: Azure AD Connect – chybami LargeObject způsobených atributem userCertificate | Dokumentace Microsoftu
description: Toto téma obsahuje kroky k nápravě chyb LargeObject způsobených atributem userCertificate.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 5361b93d24c66ef6ccb2f117e6e3a68de4c5f459
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195583"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Synchronizace Azure AD Connect: Zpracování chyb LargeObject způsobených atributem userCertificate

Azure AD se vynucuje maximální limit **15** certifikátu na hodnoty **userCertificate** atribut. Pokud Azure AD Connect exportuje objekt s více než 15 hodnot do služby Azure AD, vrátí funkce Azure AD **LargeObject** chybová zpráva:

>*"Zřízený objekt je příliš velký. Omezte počet hodnot atributů pro tento objekt. Operace se zopakuje při příštím synchronizačním cyklu..."*

Chyba LargeObject může být způsobeno další atributy AD. Pokud chcete potvrdit, to je ve skutečnosti způsobených atributem userCertificate, budete muset ověřit pro objekt v místní služby AD nebo v [hledání úložiště Metaverse Synchronization Service Manager](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch).

Pokud chcete získat seznam objektů ve vašem tenantovi, s chybami LargeObject, použijte jednu z následujících metod:

 * Pokud váš tenant je povolená pro Azure AD Connect Health pro synchronizaci, najdete [Sestava chyb synchronizace na](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync#object-level-synchronization-error-report-preview) k dispozici.
 
 * E-mail s oznámením o chybách synchronizace adresáře, který přijde na konci každé cyklus synchronizace obsahuje seznam objektů s chybami LargeObject. 
 * [Kartě provoz Synchronization Service Manager](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) zobrazí seznam objektů s chybami LargeObject vyberete-li nejnovější Export do služby Azure AD operace.
 
## <a name="mitigation-options"></a>Možnosti omezení rizik
Dokud se problém nevyřeší LargeObject, ostatní změny atributů na stejný objekt nelze exportovat do služby Azure AD. Chcete-li vyřešit chybu, můžete zvážit následující možnosti:

 * Upgrade služby Azure AD Connect k vytvoření 1.1.524.0 nebo po. Ve službě Azure AD Connect sestavení 1.1.524.0, out-of-box synchronizace, které byly aktualizovány pravidla není export atributy userCertificate a userSMIMECertificate máte víc než 15 hodnot atributů. Podrobnosti o tom, jak upgradovat Azure AD Connect, přečtěte si článek [Azure AD Connect: Upgrade z předchozí verze na nejnovější verzi](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

 * Implementace **pravidla odchozí synchronizace** ve službě Azure AD Connect, která se exportuje **místo skutečných hodnot pro objekty s více než 15 hodnot certifikát s hodnotou null**. Tato možnost je vhodná, pokud nechcete, aby žádný certifikát hodnoty, které mají být exportovány do služby Azure AD pro objekty s více než 15 hodnot. Podrobnosti o tom, jak implementovat toto synchronizační pravidlo, najdete v další části [implementace synchronizační pravidlo pro omezení export atributem userCertificate](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Snížit počet hodnot v certifikátu na místní objekt AD (15 nebo méně) tak, že odeberete hodnoty, které jsou již používá vaše organizace. To je vhodné, je-li atribut determinističtější vypršela platnost nebo se nepoužívá certifikáty. Můžete použít [tady k dispozici skript prostředí PowerShell](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) to vám umožní najít, zálohování a odstranit certifikáty s vypršenou platností v místní AD. Před odstraněním certifikáty, se doporučuje, abyste ověřili u správců infrastruktury veřejných klíčů ve vaší organizaci.

 * Konfigurace služby Azure AD Connect k vyloučení atributem userCertificate exportování do služby Azure AD. Obecně nedoporučujeme tuto možnost protože atribut můžou využívat Microsoft Online Services povolit konkrétní scénáře. Zejména:
    * Atributem userCertificate v objektu User používá Exchange Online a klienty Outlooku pro zprávu podepisování a šifrování. Další informace o této funkci najdete v článku [S/MIME pro zprávu podepisování a šifrování](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx).

    * Atributem userCertificate objektu počítače používá Azure AD umožňují Windows 10 na místním zařízení připojených k doméně připojit ke službě Azure AD. Další informace o této funkci, naleznete v článku [připojení zařízení připojených k doméně do Azure AD pro Windows 10 dojde](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementace synchronizace pravidla pro omezení export atributem userCertificate
Chcete-li vyřešit chybu LargeObject způsobených atributem userCertificate, můžete implementovat pravidlo odchozí synchronizace ve službě Azure AD Connect, který exportuje **null místo skutečných hodnot pro objekty s více než 15 hodnot certifikát**. Tato část popisuje kroky potřebné k implementaci synchronizační pravidlo pro **uživatele** objekty. Kroky mohou být přizpůsobeny pro **kontakt** a **počítače** objekty.

> [!IMPORTANT]
> Export hodnota null se odstraní certifikát hodnoty dřív úspěšně exportovány do služby Azure AD.

Postup lze shrnout jako:

1. Zakázat Plánovač synchronizace a ověřte, že neexistuje žádná synchronizace probíhá.
3. Pro atribut userCertificate najdete existující pravidlo odchozí synchronizace.
4. Vytvořte pravidlo odchozí synchronizace vyžaduje.
5. Ověřte nové pravidlo synchronizace na existující objekt s chybou LargeObject.
6. Platí pro zbývající objekty s chybou LargeObject nové pravidlo synchronizace.
7. Ověřte, neexistují žádné neočekávané změny čekající na export do služby Azure AD.
8. Exportujte změny do služby Azure AD.
9. Povolte Plánovač synchronizace.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1. Zakázat Plánovač synchronizace a ověřte, že není žádná probíhající synchronizace
Ujistěte se, že žádná synchronizace bude probíhat, když jste uprostřed implementace nové pravidlo synchronizace, aby se zabránilo neúmyslnému změny exportované do služby Azure AD. Chcete-li zakázat plánovač integrované synchronizace:
1. Spusťte relaci Powershellu na serveru služby Azure AD Connect.

2. Zakažte plánované synchronizaci spuštěním rutiny: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> V předchozích krocích platí pouze pro novější verze (1.1.xxx.x) služby Azure AD Connect s využitím integrované plánovače. Pokud používáte starší verze (1.0.xxx.x) služby Azure AD Connect, používající Plánovač úloh Windows, nebo používáte vlastní vlastní plánovač (není běžné) k aktivaci pravidelnou synchronizaci, musíte je zakázat odpovídajícím způsobem.

3. Spustit **Synchronization Service Manager** tak, že přejdete na ZAČÁTKU → synchronizační služba.

4. Přejděte **operace** kartu a potvrdit, neexistuje žádná operace, jejichž stav je *"probíhá."*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Krok 2. Najít existující pravidlo odchozí synchronizace pro atributem userCertificate
Měla by existovat existující synchronizační pravidlo, které je povolená a nakonfigurovaná tak, aby export atributem userCertificate pro uživatelské objekty do služby Azure AD. Vyhledejte pravidlo synchronizace a zjistěte jeho **prioritu** a **filtr oborů** konfigurace:

1. Spustit **Editor pravidel synchronizace** tak, že přejdete na ZAČÁTKU → Editor pravidel synchronizace.

2. Konfigurace filtrů hledání s použitím následujících hodnot:

    | Atribut | Hodnota |
    | --- | --- |
    | Směr |**Odchozí** |
    | Typ objektu MV |**Osoba** |
    | Spojovací čára |*název konektoru Azure AD* |
    | Typ objektu konektoru |**Uživatel** |
    | Atribut MV |**userCertificate** |

3. Pokud používáte OOB (out-of-box) synchronizační pravidla pro konektor služby Azure AD pro export userCertficiate atribut pro uživatelské objekty, měli byste obdržet *"Navýšení kapacity na AAD – uživatel ExchangeOnline"* pravidlo.
4. Poznamenejte si **prioritu** hodnotu toto synchronizační pravidlo.
5. Vyberte synchronizační pravidlo a klikněte na tlačítko **upravit**.
6. V *"Upravit vyhrazené pravidlo potvrzení"* automaticky otevíraná okna, klikněte na tlačítko **ne**. (Buďte bez obav, nebudeme měnit toto synchronizační pravidlo).
7. Na obrazovce pro úpravy, vyberte **Scoping filtr** kartu.
8. Poznamenejte si konfiguraci oboru filtru. Pokud používáte pravidlo synchronizace OOB, měl by přesně existovat **jednoho oboru filtru skupiny obsahující dvou klauzulí**, včetně:

    | Atribut | Operátor | Hodnota |
    | --- | --- | --- |
    | sourceObjectType | ROVNO | Uživatel |
    | cloudMastered | NOTEQUAL | True |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Krok 3. Vytvořit požadované pravidlo odchozí synchronizace
Nové pravidlo synchronizace musí mít stejné **filtr oborů** a **vyšší prioritu** než existující synchronizační pravidlo. Tím se zajistí, že se nové pravidlo synchronizace se vztahuje na stejnou sadu objektů jako existující pravidlo synchronizace a přepíše existující synchronizační pravidlo pro atribut userCertificate. Vytvoření pravidla synchronizace:
1. V editoru pravidel synchronizace, klikněte na tlačítko **přidat nové pravidlo** tlačítko.
2. V části **karta popis**, zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | --- | --- | --- |
    | Název | *Zadejte název* | Například *"Out aad – vlastní přepsání pro userCertificate"* |
    | Popis | *Zadejte popis* | Například *"Pokud atributem userCertificate má více než 15 hodnot, export hodnotu NULL."* |
    | Připojený systém | *Vyberte konektor Azure AD* |
    | Typ objektu systému připojené | **Uživatel** | |
    | Typ objektu úložiště Metaverse | **Osoba** | |
    | Typ odkazu | **Spojení** | |
    | Priorita | *Zvolte číslo v rozmezí 1 až 99* | Číslo zvolený nesmí používat žádné existující pravidlo synchronizace a má nižší hodnotu (a tedy vyšší prioritu) než existující synchronizační pravidlo. |

3. Přejděte **Scoping filtr** kartu a implementovat stejný filtr oborů pomocí existující synchronizační pravidlo.
4. Přeskočit **připojení pravidla** kartu.
5. Přejděte **transformace** karta pro přidání nové transformace pomocí následující konfigurace:

    | Atribut | Hodnota |
    | --- | --- |
    | Typ toku |**Výraz** |
    | Cílový atribut |**userCertificate** |
    | Zdrojový atribut |*Použijte následující výraz*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Klikněte na tlačítko **přidat** tlačítko vytvoříte pravidlo synchronizace.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Krok 4. Ověření nové pravidlo synchronizace na existující objekt s chybou LargeObject
Toto je ověřit, že synchronizační pravidlo vytvořené funguje správně na existující objekt AD s chybou LargeObject předtím, než ho použijete na jiné objekty:
1. Přejděte **operace** kartu v Synchronization Service Manager.
2. Vyberte poslední Export do operace služby Azure AD a klikněte na některý z objektů s chybami LargeObject.
3.  Na zobrazené obrazovce vlastnosti objektu prostoru konektoru, klikněte na **ve verzi Preview** tlačítko.
4. V automaticky otevírané obrazovky ve verzi Preview, vyberte **úplné synchronizace** a klikněte na tlačítko **potvrzení změn ve verzi Preview**.
5. Zavřete náhled obrazovky a obrazovky Vlastnosti objektu prostoru konektoru.
6. Přejděte **konektory** kartu v Synchronization Service Manager.
7. Klikněte pravým tlačítkem na **Azure AD** konektoru a vyberte **spuštění...**
8. V místní nabídce spusťte konektor, vyberte **exportovat** kroku a klikněte na tlačítko **OK**.
9. Počkejte, Export do služby Azure AD pro dokončení a potvrďte, že se nezobrazí žádná další chyba LargeObject pro tento konkrétní objekt.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Krok 5. Použít nové pravidlo synchronizace s chybou LargeObject ve zbývajících objektech
Jakmile se přidala pravidla synchronizace, je potřeba spustit krok úplná synchronizace v konektoru služby AD:
1. Přejděte **konektory** kartu v Synchronization Service Manager.
2. Klikněte pravým tlačítkem na **AD** konektoru a vyberte **spuštění...**
3. V místní nabídce spusťte konektor, vyberte **úplnou synchronizaci** kroku a klikněte na tlačítko **OK**.
4. Počkejte, krok úplnou synchronizaci pro dokončení.
5. Pokud máte více než jeden AD konektorů, opakujte předchozí postup pro zbývající AD konektory. Obvykle se vyžadují více konektorů, pokud máte více místních adresářů.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Krok 6. Ověřte neexistují žádné neočekávané změny čekající na export do služby Azure AD
1. Přejděte **konektory** kartu v Synchronization Service Manager.
2. Klikněte pravým tlačítkem na **Azure AD** konektoru a vyberte **Search Connector Space**.
3. V místní nabídce Search Connector Space:
    1. Nastavit obor **čekající na Export**.
    2. Zkontrolujte všechny 3 zaškrtávací políčka, včetně **přidat**, **změnit** a **odstranit**.
    3. Klikněte na tlačítko **hledání** tlačítko budou vráceny všechny objekty s změny čekající na export do služby Azure AD.
    4. Ověřte, že nejsou žádné neočekávaným změnám. Přezkoumat změny pro daný objekt, dvakrát klikněte na objekt.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Krok 7. Exportovat změny do služby Azure AD
Chcete-li exportovat změny do služby Azure AD:
1. Přejděte **konektory** kartu v Synchronization Service Manager.
2. Klikněte pravým tlačítkem na **Azure AD** konektoru a vyberte **spuštění...**
4. V místní nabídce spusťte konektor, vyberte **exportovat** kroku a klikněte na tlačítko **OK**.
5. Export do služby Azure AD pro dokončení a potvrďte, že zde nejsou žádné další chyby LargeObject počkejte.

### <a name="step-8-re-enable-sync-scheduler"></a>Krok 8. Povolte Plánovač synchronizace
Teď, když se problém vyřeší, povolte Plánovač integrované synchronizace:
1. Spusťte relaci Powershellu.
2. Spuštěním rutiny povolte plánované synchronizaci: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> V předchozích krocích platí pouze pro novější verze (1.1.xxx.x) služby Azure AD Connect s využitím integrované plánovače. Pokud používáte starší verze (1.0.xxx.x) služby Azure AD Connect, používající Plánovač úloh Windows, nebo používáte vlastní vlastní plánovač (není běžné) k aktivaci pravidelnou synchronizaci, musíte je zakázat odpovídajícím způsobem.

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).


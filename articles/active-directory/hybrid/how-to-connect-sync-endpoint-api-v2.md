---
title: Inverze Public Preview pro koncový bod služby Azure AD Connect Sync v2 | Microsoft Docs
description: Tento dokument popisuje aktualizace rozhraní API koncových bodů služby Azure AD Connect Sync v2.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f4eba1b48b651c8efe9e9d737e226727cb244fb
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662473"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>Rozhraní API koncového bodu služby Azure AD Connect Sync v2 (Public Preview) 
Společnost Microsoft nasadila nový koncový bod (rozhraní API) pro Azure AD Connect, který vylepšuje výkon operací synchronizační služby Azure Active Directory. Díky použití nového koncového bodu v2 budete mít při exportu a importu do Azure AD patrné zvýšení výkonu. Tento nový koncový bod podporuje následující:
    
 -  synchronizace skupin s až 250 tisíc členy
 - zisky z výkonu při exportu a importu do Azure AD
 
> [!NOTE]
> V současné době nový koncový bod nemá nakonfigurovanou omezení velikosti skupiny pro Microsoft 365 skupiny, které jsou zapsány zpět. To může mít vliv na latenci služby Active Directory a synchronizaci cyklů. Doporučuje se postupně zvyšovat velikosti skupin.  


## <a name="pre-requisites"></a>Požadavky  
Aby bylo možné použít nový koncový bod v2, budete muset použít [Azure AD Connect verze 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) nebo novější a postupovat podle níže uvedených kroků nasazení a povolit koncový bod v2 pro váš Azure AD Connect Server.   

>[!NOTE]
>V současné době je tato verze Public Preview dostupná jenom v globálním cloudu Azure a není dostupná pro [národní](../develop/authentication-national-cloud.md)cloudy.

### <a name="public-preview-limitations"></a>Omezení veřejné verze Preview  
I když tato verze prošla rozsáhlým testováním, může dojít k problémům. Jedním z cílů této verze Public Preview je vyhledat a opravit takové problémy.  

>[!IMPORTANT]
> I když je pro tuto verzi veřejné verze Preview k dispozici podpora, společnost Microsoft nemusí vždy řešit všechny problémy, se kterými se můžete setkat hned. Z tohoto důvodu doporučujeme, abyste před nasazením této verze v produkčním prostředí používali svůj nejlepší odhad. 

## <a name="deployment-guidance"></a>Pokyny k nasazení 
Pro použití koncového bodu v2 budete muset nasadit [Azure AD Connect verze 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) nebo novější. Použijte odkaz určený ke stažení. 

Pro zavedení nového koncového bodu ve vašem prostředí doporučujeme postupovat podle metody [migrace](./how-to-upgrade-previous-version.md#swing-migration) přístupnosti. V takovém případě bude v případě jasného pohotovostního plánu v případě nutnosti důležité vrácení zpět. Následující příklad ukazuje, jak lze v tomto scénáři použít migraci za použití. Další informace o metodě nasazení migrace po migraci najdete v zadaném odkazu. 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>Zapojení do migrace pro nasazení koncového bodu v2
Následující postup vás provede nasazením koncového bodu v2 pomocí metody dráhy.

1. Nasaďte koncový bod v2 na aktuální pracovní server. Tento server bude v níže uvedených krocích znám jako **Server v2** . Aktuální aktivní server bude pokračovat ve zpracování produkční úlohy pomocí koncového bodu V1, který se bude jmenovat níže na **serveru v1** .
1. Ověřte, že **Server v2** stále zpracovává importy podle očekávání. V této fázi se velké skupiny nezřídí pro Azure AD ani on-Prem AD, ale budete moct ověřit, že upgrade nevedl k žádnému neočekávanému dopadu na stávající proces synchronizace. 
2. Po dokončení ověření přepněte **Server v2** tak, aby byl aktivním serverem a **Server v1** jako pracovní server. V tuto chvíli budou velké skupiny, které jsou v oboru, které se mají synchronizovat, zřízené ve službě Azure AD a velké Microsoft 365 sjednocené skupiny se zřídí ve službě AD, pokud je povolen zpětný zápis skupiny.
3. Ověřte, že **Server v2** provádí a úspěšně zpracovává velké skupiny. V tomto kroku se můžete rozhodnout, že v tomto kroku budete sledovat proces synchronizace za určitou dobu.
  >[!NOTE]
  > Pokud potřebujete přejít zpátky k předchozí konfiguraci, můžete provést migraci z **verze V2** na **Server v1**zpátky. Vzhledem k tomu, že koncový bod v1 nepodporuje skupiny s více než 50 tis členy, bude následně odstraněna jakákoli velká skupina, která byla zřízena Azure AD Connect v Azure AD nebo v Prem AD. 
4. Jakmile budete mít jistotu, že používáte koncový bod v2, upgradujte **Server v1** a začněte používat koncový bod v2. 
 

## <a name="expectations-of-performance-impact"></a>Očekávané dopady na výkon  
Při použití koncového bodu v2 jsou nárůsty výkonu funkce počtu synchronizovaných skupin, velikosti těchto skupin a jejich skupin (aktivita vzniklá přidáváním a odebíráním uživatelů jako členy skupiny). Použití nového koncového bodu, aniž by se zvýšil počet, velikost nebo četnost synchronizovaných skupin, by měly mít za následek kratší dobu pro export a import do Azure AD. 
 
Zvýšení výkonu však může být při synchronizaci velkých skupin na negaci při dalším zpracování. Můžete zvýšit celkovou dobu synchronizace přidáním příliš velkého počtu velkých skupin do procesu synchronizace.  

Chcete-li získat lepší přehled o tom, jak bude přidání nových skupin mít vliv na výkon synchronizace, doporučujeme začít synchronizací pouze několika velkých skupin s méně než 100 tisíc členy. Počet a velikost skupin pak můžete zvýšit tak, že více z nich rozvedete do rozsahu, prostřednictvím organizační jednotky, atributu nebo maxima filtrování velikosti skupin. Vylepšení výkonu se budou provádět na úkolech exportu a importu pro konektor Azure AD, nikoli v místní službě AD Connector. 

## <a name="deployment-step-by-step"></a>Postup nasazení krok za krokem 
Následující tři fáze jsou podrobnějším příkladem nasazení nového koncového bodu v2.  Použijte fáze jako vodítko pro nasazení.

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>Fáze 1 – instalace a ověření Azure AD Connect 
Doporučuje se nejdřív provést kroky k instalaci nebo upgradu na [verzi Azure AD Connect 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) nebo novější a před přechodem do druhé fáze, kde budete povolit koncový bod v2, ověřit proces synchronizace. Na serveru Azure AD Connect: 


1. Volitelné Pořídit zálohu databáze 
2. Nainstalujte nebo upgradujte na [verzi Azure AD Connect 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) nebo novější.
3. Ověření instalace 

### <a name="phase-2--enable-the-v2-endpoint"></a>Fáze 2 – povolení koncového bodu v2 
Dalším krokem je povolení koncového bodu v2. 

> [!NOTE]
> Po povolení koncového bodu v2 pro váš server budete moci zobrazit některá vylepšení výkonu pro stávající úlohy. Přesto nebudete moct synchronizovat skupiny s dalšími členy, které 50 tis. 

Chcete-li přepnout na koncový bod v2, použijte následující postup: 

1. Otevřete příkazový řádek PowerShellu jako správce. 
2. Zakažte Plánovač synchronizace po ověření, že neběží žádné operace synchronizace: 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. Importovat nový modul: 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  Přepněte na koncový bod v2:

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
Nyní jste povolili koncový bod v2 pro váš server. Před přechodem na další fázi, kde zvýšíte limit velikosti skupiny, se po povolení koncového bodu v2 ujistěte, že nedošlo k neočekávaným výsledkům. 
>[!NOTE]
>Cesty k souboru nebo modulu můžou používat jiné písmeno jednotky v závislosti na instalační cestě, která se poskytuje při instalaci Azure AD Connect. 


### <a name="phase-3--increase-the-group-membership-limit"></a>Fáze 3 – zvýšení limitu členství ve skupině 
Až ověříte, že je služba spuštěná, aniž by došlo k neočekávaným výsledkům, můžete pokračovat ve zvýšení limitu členství ve skupině. Doporučuje se nejdřív zvýšit limit členství na trochu vyšší hodnotu, e g. 75K členové, aby viděli větší skupiny, které se synchronizují do Azure AD. Až budete s výsledky spokojeni, můžete omezení členů dále zvýšit.  

Maximální limit je 250 tisíc členů na skupinu. 

K navýšení limitu členství můžete použít následující kroky:  

1. Otevřít Editor pravidel synchronizace Azure AD 
2. V editoru vyberte **odchozí** pro směr 
3. Klikněte na možnost **ven do AAD –** pravidlo synchronizace připojení ke skupině. 
4. Klikněte na **Edit** tlačítko Upravit ![ Upravit pravidlo synch.](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. Kliknutím na tlačítko **Ano** zakážete výchozí pravidlo a vytvoříte upravitelnou kopii.
 ![Upravit pravidlo synchronizace](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. V automaticky otevíraném okně na stránce **Popis** nastavte prioritu na dostupnou hodnotu v rozmezí 1 až 99 ![ Upravit pravidlo synch.](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. Na stránce **transformace** aktualizujte **zdrojovou** hodnotu pro transformaci **členů** a nahraďte hodnotu ' 50000 ' hodnotou v rozmezí 50001 až 250000. Tato náhrada zvýší maximální velikost členství skupin, které se budou synchronizovat do Azure AD. Doporučujeme začít s řadou 100 tisíc, abyste pochopili, jaký dopad synchronizace velkých skupin bude na výkon synchronizace. 
 
 **Příklad** 
 
 `IIF((ValueCount("member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![Upravit pravidlo synchronizace](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. Kliknutí na Uložit 
10. Otevřete příkazový řádek PowerShellu pro správu. 
11. Opětovné povolení plánovače synchronizace 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Pokud Azure AD Connect Health není povolená, změňte nastavení protokolu událostí aplikace systému Windows tak, aby se protokoly archivoval, místo jejich přepsání. Protokoly se můžou použít k usnadnění budoucího úsilí při odstraňování potíží. 

>[!NOTE]
> Po povolení nového koncového bodu se můžou v konektoru AAD zobrazit další chyby při exportu s názvem "rozlišující název-atributy-selhání". Pro každou chybu s ID 6949 bude k dispozici odpovídající položka protokolu událostí. Chyby jsou informativní a nenaznačují problém s instalací, ale místo toho proces synchronizace nemohly do skupiny ve službě Azure AD přidat určité členy, protože samotný členský objekt nebyl synchronizován do Azure AD. 

Nový kód koncového bodu v2 zpracovává některé typy chyb exportu trochu odlišně od toho, jak byl kód v1.  Při použití koncového bodu v2 se může zobrazit více informativních chybových zpráv. 

>[!NOTE]
> Při upgradu Azure AD Connect zajistěte, aby byly kroky ve fázi 2 znovu spouštěny, protože změny nejsou zachovány v rámci procesu upgradu. 

Během následného nárůstu na omezení počtu členů skupiny v pravidle **pro synchronizaci z provozu do služby AAD – připojení ke skupině** není úplná synchronizace nutná, takže se můžete rozhodnout pro potlačení úplné synchronizace spuštěním následujícího příkazu v PowerShellu. 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> Pokud máte Microsoft 365 sjednocené skupiny, které mají více než 50 tis členové, budou se skupiny číst do Azure AD Connect a pokud je povolen zpětný zápis skupiny, budou zapsány do místní služby AD. 

## <a name="rollback"></a>Návrat 
Pokud jste povolili koncový bod v2 a potřebujete provést vrácení zpět, postupujte takto: 

1. Na serveru Azure AD Connect: a. Volitelné Pořídit zálohu databáze 
2. Otevřete příkazový řádek PowerShellu pro správu:
3. Zakázat Plánovač synchronizace po ověření, že neběží žádné operace synchronizace
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. Přepněte na koncový bod v1 *. 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Otevřít Editor pravidel synchronizace Azure AD 
6. Odstraní upravitelnou kopii pravidla synchronizace typu **out-to-AAD – připojení ke skupině** . 
7. Povolit výchozí kopii pravidla synchronizace **pro připojení k AAD – spojení se skupinou** 
8. Otevřete příkazový řádek PowerShellu pro správu. 
9. Opětovné povolení plánovače synchronizace 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> Když přepnete zpátky z koncových bodů v2 na V1, skupiny synchronizované s více než 50 tis členy se po spuštění úplné synchronizace odstraní pro obě skupiny AD zřízené na Azure AD a Microsoft 365 sjednocené skupiny zřízené pro AD. 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy  
**Otázka: může zákazník použít tuto funkci v produkčním prostředí?**  
</br>Ano, tato možnost se dá použít v produkčním prostředí s upozorněním, jak je uvedeno výše.
 
**Otázka: kdo může kontaktovat zákazníka, když se něco pokazilo?**  
</br>Pokud potřebujete podporu při používání této funkce, měli byste otevřít případ podpory. 
 
**Otázka: je možné očekávat časté aktualizace verze Public Preview?**  
</br>Během Public Preview existuje omezená míra probíhajících změn.Toto riziko byste měli vyhodnotit při nasazování Public Previewch funkcí v produkčním prostředí.  
 
**Otázka: čas do dalšího milníku?**  
</br>Je možné, že se funkce Public Preview před dalším milníkem odeberou a případně přenavrhují.  
 
## <a name="next-steps"></a>Další kroky

* [Azure AD Connect synchronizace: pochopení a přizpůsobení synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
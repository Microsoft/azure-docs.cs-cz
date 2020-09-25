---
title: 'Azure AD Connect: upgrade z předchozí verze | Microsoft Docs'
description: Vysvětluje různé metody upgradu na nejnovější verzi Azure Active Directory Connect, včetně místního upgradu a migrace.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b78d3cab17b0cc4085c824cf35d4c6037f0e2af5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319856"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Upgrade z předchozí verze na nejnovější verzi
Toto téma popisuje různé metody, které můžete použít k upgradu instalace služby Azure Active Directory (Azure AD) Connect na nejnovější verzi. Doporučujeme, abyste si zachovali aktuální verze Azure AD Connect. Při provádění podstatné změny konfigurace můžete také použít kroky v části kroková [migrace](#swing-migration) .

>[!NOTE]
> V současné době se podporuje upgrade z jakékoli verze Azure AD Connect na aktuální verzi. Místní upgrady DirSync nebo ADSync se nepodporují a vyžaduje se migrace.  Pokud chcete upgradovat z DirSync, přečtěte si téma [upgrade z nástroje Azure AD Sync (DirSync)](how-to-dirsync-upgrade-get-started.md) nebo část pro [migraci](#swing-migration) .  </br>V praxi můžou zákazníci s extrémně starými verzemi narazit na problémy, které přímo nesouvisejí s Azure AD Connect. Servery, které byly v produkčním prostředí po dobu několika let, obvykle mají pro ně aplikováno několik oprav, a ne všechny z nich, pro které se dají použít.  Obecně platí, že zákazníci, kteří byli upgradováni v 12-18 měsíců, by měli místo toho vzít v úvahu upgrade, protože se jedná o nejužitečnější a nejnižší rizikové možnosti.

Pokud chcete upgradovat z DirSync, přečtěte si téma [upgrade z nástroje Azure AD Sync (DirSync)](how-to-dirsync-upgrade-get-started.md) .

K upgradu Azure AD Connect můžete použít několik různých strategií.

| Metoda | Popis |
| --- | --- |
| [Automatický upgrade](how-to-connect-install-automatic-upgrade.md) |Toto je nejjednodušší způsob pro zákazníky, kteří mají expresní instalaci. |
| [Místní upgrade](#in-place-upgrade) |Pokud máte jeden server, můžete upgradovat instalaci na stejném serveru na místě. |
| [Postupná migrace](#swing-migration) |Se dvěma servery můžete připravit jeden ze serverů s novou verzí nebo konfigurací a až budete připravení, můžete aktivní server změnit. |

Informace o oprávněních najdete v tématu [oprávnění požadovaná pro upgrade](reference-connect-accounts-permissions.md#upgrade).

> [!NOTE]
> Až zapnete nový Azure AD Connect Server, aby se spouštěly změny ve službě Azure AD, nemusíte se vrátit zpátky na používání DirSync nebo Azure AD Sync. Přechod z Azure AD Connect na starší verze klientů, včetně DirSync a Azure AD Sync, není podporován a může vést k problémům, jako je například ztráta dat ve službě Azure AD.

## <a name="in-place-upgrade"></a>Místní upgrade
Místní upgrade funguje pro přechod z Azure AD Sync nebo Azure AD Connect. Nefunguje pro přesun z DirSync nebo pro řešení s konektorem Forefront Identity Manager (FIM) + Azure AD.

Tato metoda je preferována v případě, že máte jeden server a méně než přibližně 100 000 objektů. Pokud jsou k dispozici změny v rámci předdefinovaných pravidel synchronizace, po upgradu dojde k úplnému importu a úplné synchronizaci. Tato metoda zajistí, že se nová konfigurace použije u všech existujících objektů v systému. Tento běh může trvat několik hodin, v závislosti na počtu objektů, které jsou v rozsahu synchronizačního modulu. Standardní Plánovač rozdílové synchronizace (který se ve výchozím nastavení synchronizuje každých 30 minut) je pozastaven, ale synchronizace hesel pokračuje. V průběhu víkendu můžete zvážit místní upgrade. Pokud se v nové verzi Azure AD Connect nemění nastavení dopředných verzí, pak se místo toho spustí normální rozdílový import/synchronizace.  
![Místní upgrade](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Pokud jste provedli změny v rámci předdefinovaných pravidel synchronizace, tato pravidla se nastaví zpátky na výchozí konfiguraci při upgradu. Abyste se ujistili, že je konfigurace mezi upgrady zachovaná, ujistěte se, že jste provedli změny, jak jsou popsané v tématu [osvědčené postupy pro změnu výchozí konfigurace](how-to-connect-sync-best-practices-changing-default-configuration.md).

Během místního upgradu můžou být zavedené změny, které vyžadují konkrétní synchronizační aktivity (včetně kroku úplného importu a úplné synchronizace), které se spustí po dokončení upgradu. Pokud chcete takové aktivity odložit, přečtěte si část [postup odložení úplné synchronizace po upgradu](#how-to-defer-full-synchronization-after-upgrade).

Pokud používáte Azure AD Connect s nestandardním konektorem (například obecný konektor LDAP a obecný konektor SQL), musíte aktualizovat odpovídající konfiguraci konektoru v [Synchronization Service Manager](./how-to-connect-sync-service-manager-ui-connectors.md) po místním upgradu. Podrobnosti o tom, jak aktualizovat konfiguraci konektoru, najdete v části [konektor – Historie vydání verze – řešení potíží](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#troubleshooting). Pokud neaktualizujete kroky pro konfiguraci, import a export, nebudou pro konektor správně fungovat. V protokolu událostí aplikace se zobrazí následující chybová zpráva *verze sestavení v konfiguraci konektoru AAD ("X.X.xxx. X ") je starší než skutečná verze (" X.X.XXX. X ") adresáře" C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll ".*

## <a name="swing-migration"></a>Postupná migrace
Pokud máte složité nasazení nebo mnoho objektů, může být nepraktické provést místní upgrade v živém systému. Pro některé zákazníky může tento proces trvat několik dní – a během této doby se nezpracují žádné rozdílové změny. Tuto metodu můžete použít také v případě, že plánujete udělat podstatné změny v konfiguraci a chcete si je vyzkoušet před jejich odesláním do cloudu.

Doporučeným způsobem pro tyto scénáře je použití migrace. Potřebujete (aspoň) dva servery – jeden aktivní server a jeden pracovní server. Aktivním serverem (zobrazeným s plnými modrými čárami na následujícím obrázku) je zodpovědný za aktivní provozní zatížení. Pracovní server (zobrazený pomocí přerušované fialové čáry) je připravený s novou verzí nebo konfigurací. Pokud je to plně připravené, tento server se vystavil jako aktivní. Předchozí aktivní server, který má teď nainstalovanou starou verzi nebo konfiguraci, se provede na přípravném serveru a upgraduje se.

Dva servery můžou používat různé verze. Například aktivní server, který chcete vyřadit z provozu, může používat Azure AD Sync a nový pracovní server může používat Azure AD Connect. Pokud k vývoji nové konfigurace použijete migraci za použití, je dobré mít na obou serverech stejné verze.  
![Pracovní server](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Někteří zákazníci chtějí mít pro tento scénář tři nebo čtyři servery. Po upgradu přípravného serveru nemáte k dispozici záložní server pro [zotavení po havárii](how-to-connect-sync-staging-server.md#disaster-recovery). Se třemi nebo čtyřmi servery můžete připravit jednu sadu primárních nebo pohotovostních serverů s novou verzí, což zajistí, že bude vždy pracovní server, který je připravený k převzetí služeb při selhání.

Tyto kroky také můžete použít k přesunu z Azure AD Sync nebo řešení pomocí konektoru FIM + Azure AD. Tyto kroky nefungují pro DirSync, ale stejný způsob migrace (označovaný také jako paralelní nasazení) s kroky pro DirSync je v [upgradu Azure Active Directory Sync (DirSync)](how-to-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>K upgradu použijte migraci na novou dráhu.
1. Pokud na obou serverech použijete Azure AD Connect a naplánujete pouze změnu konfigurace, ujistěte se, že váš aktivní server a přípravný server používají stejnou verzi. Díky tomu je snazší porovnat rozdíly později. Pokud provádíte upgrade z Azure AD Sync, tyto servery mají různé verze. Pokud provádíte upgrade ze starší verze Azure AD Connect, je dobré začít se dvěma servery, které používají stejnou verzi, ale není to nutné.
2. Pokud jste udělali vlastní konfiguraci a váš pracovní server ho nemá, postupujte podle kroků v části [přesunutí vlastní konfigurace z aktivního serveru na pracovní server](#move-a-custom-configuration-from-the-active-server-to-the-staging-server).
3. Pokud upgradujete ze starší verze Azure AD Connect, upgradujte pracovní server na nejnovější verzi. Pokud přesouváte z Azure AD Sync, nainstalujte Azure AD Connect na svém přípravném serveru.
4. Umožněte, aby synchronizační modul spouštěl úplný Import a úplnou synchronizaci na vašem přípravném serveru.
5. Ověřte, že nová konfigurace nezpůsobila žádné neočekávané změny pomocí postupu v části "ověřit" v části [ověření konfigurace serveru](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server). Pokud něco není podle očekávání, opravte ho, spusťte import a synchronizaci a ověřte data, dokud to nebudete mít, podle pokynů.
6. Přepněte pracovní server na aktivní server. Toto je finální krok "přepnutí aktivního serveru" v tématu [ověření konfigurace serveru](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. Pokud provádíte upgrade Azure AD Connect, upgradujte Server, který je teď v pracovním režimu, na nejnovější verzi. Použijte stejný postup jako předtím, abyste získali upgrade dat a konfigurace. Pokud jste provedli upgrade z Azure AD Sync, můžete nyní vypnout a vyřadit z provozu starý server.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Přesunutí vlastní konfigurace z aktivního serveru na pracovní server
Pokud jste provedli změny konfigurace aktivního serveru, musíte se ujistit, že se na pracovním serveru uplatní stejné změny. Pro usnadnění tohoto přesunu můžete použít [konfigurační dokument Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

Můžete přesunout vlastní pravidla synchronizace, která jste vytvořili pomocí prostředí PowerShell. U obou systémů musíte použít jiné změny a nemůžete migrovat změny. [Konfigurační dokumentace](https://github.com/Microsoft/AADConnectConfigDocumenter) vám může usnadnit porovnávání obou systémů, abyste se ujistili, že jsou identické. Nástroj může také pomáhat při automatizaci kroků uvedených v této části.

Na obou serverech je potřeba nakonfigurovat následující věci:

* Připojení ke stejným doménovým strukturám
* Jakékoli filtrování domén a organizačních jednotek
* Stejné volitelné funkce, například synchronizace hesel a zpětný zápis hesla

**Přesunout vlastní pravidla synchronizace**  
Pokud chcete přesunout vlastní pravidla synchronizace, udělejte toto:

1. Otevřete **Editor pravidel synchronizace** na aktivním serveru.
2. Vyberte vlastní pravidlo. Klikněte na **Exportovat**. Tím se otevře okno poznámkového bloku. Uložte dočasný soubor s příponou PS1. Tím se vytvoří skript PowerShellu. Zkopírujte soubor PS1 na pracovní server.  
   ![Export pravidla synchronizace](./media/how-to-upgrade-previous-version/exportrule.png)
3. Identifikátor GUID konektoru se liší od přípravného serveru a musíte ho změnit. Chcete-li získat identifikátor GUID, spusťte **Editor pravidel synchronizace**, vyberte jedno z předdefinovaných pravidel, která reprezentují stejný připojený systém, a klikněte na tlačítko **exportovat**. Nahraďte identifikátor GUID v souboru PS1 identifikátorem GUID z přípravného serveru.
4. V příkazovém řádku PowerShellu spusťte soubor PS1. Tím se vytvoří pravidlo vlastního synchronizace na pracovním serveru.
5. Tento postup opakujte pro všechna vaše vlastní pravidla.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Odložit úplnou synchronizaci po upgradu
Během místního upgradu se můžou provést změny, které vyžadují konkrétní synchronizační aktivity (včetně kroku úplného importu a úplné synchronizace). Například změny schématu konektoru vyžadují **úplný krok importu** a dopředné změny synchronizačního pravidla vyžadují, aby byl v ovlivněných konektorech spuštěný krok **úplné synchronizace** . Během upgradu Azure AD Connect určuje, které synchronizační aktivity se vyžadují a zaznamenává je jako *přepsání*. V následujícím cyklu synchronizace povede Plánovač synchronizace Tato přepsání a spustí je. Po úspěšném provedení přepsání dojde k jeho odebrání.

Mohou nastat situace, kdy nechcete, aby tato přepsání byla provedena ihned po upgradu. Máte například spoustu synchronizovaných objektů a chcete, aby tyto kroky synchronizace probíhaly po pracovní době. Odebrání těchto přepsání:

1. Během upgradu **zrušte zaškrtnuté políčko** **po dokončení konfigurace spustit proces synchronizace**. Tím se zakáže Plánovač synchronizace a zabrání se automatickému zacyklení procesu synchronizace před odebráním přepsání.

   ![Snímek obrazovky, který zvýrazní možnost spustit proces synchronizace po dokončení konfigurace, kterou je třeba vymazat.](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. Po dokončení upgradu spusťte následující rutinu, abyste zjistili, jaké přepsání bylo přidáno: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > Přepsání jsou specifická pro konektor. V následujícím příkladu byl do místního konektoru služby AD i z konektoru služby Azure AD přidán úplný krok importu a krok úplné synchronizace.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Poznamenejte si existující přepsání, která byla přidána.
   
4. Chcete-li odebrat přepsání pro úplné import i úplnou synchronizaci s libovolným konektorem, spusťte následující rutinu: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Chcete-li odebrat přepsání na všech konektorech, spusťte následující skript prostředí PowerShell:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Chcete-li pokračovat v plánovači, spusťte následující rutinu: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Nezapomeňte provést požadované kroky synchronizace při nejbližším pohodlí. Synchronization Service Manager pomocí rutiny Set-ADSyncSchedulerConnectorOverride můžete ručně provést tyto kroky a přidat přepsání zpět.

Chcete-li přidat přepsání pro úplné import i úplnou synchronizaci s libovolným konektorem, spusťte následující rutinu:  `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Řešení potíží
Následující část obsahuje řešení potíží a informace, které můžete použít, pokud dojde k potížím s upgradem Azure AD Connect.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Azure Active Directory konektoru chybí chyba během upgradu Azure AD Connect

Při upgradu Azure AD Connect z předchozí verze se může při zahájení upgradu narazit na následující chybu. 

![Chyba](./media/how-to-upgrade-previous-version/error1.png)

K této chybě dochází, protože konektor Azure Active Directory s identifikátorem b891884f-051e-4A83-95af-2544101c9083 neexistuje v aktuální konfiguraci Azure AD Connect. Pokud chcete ověřit, že se jedná o tento případ, otevřete okno PowerShellu, spusťte rutinu. `Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

```
PS C:\> Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
Get-ADSyncConnector : Operation failed because the specified MA could not be found.
At line:1 char:1
+ Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ReadError: (Microsoft.Ident...ConnectorCmdlet:GetADSyncConnectorCmdlet) [Get-ADSyncConne
   ctor], ConnectorNotFoundException
    + FullyQualifiedErrorId : Operation failed because the specified MA could not be found.,Microsoft.IdentityManageme
   nt.PowerShell.Cmdlet.GetADSyncConnectorCmdlet

```

Rutina PowerShellu hlásí chybu **, že zadaný ma nešlo najít**.

Důvodem je, že aktuální konfigurace Azure AD Connect není podporována pro upgrade. 

Pokud chcete nainstalovat novější verzi Azure AD Connect: zavřete průvodce Azure AD Connect, odinstalujte existující Azure AD Connect a proveďte čistou instalaci novějšího Azure AD Connect.



## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [integraci místních identit s Azure Active Directory](whatis-hybrid-identity.md).
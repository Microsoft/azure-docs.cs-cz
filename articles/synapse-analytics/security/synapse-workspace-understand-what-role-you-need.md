---
title: Pochopení rolí vyžadovaných k provádění běžných úloh v synapse
description: Tento článek popisuje, které předdefinované role RBAC synapse je potřeba k tomu, aby se prováděly konkrétní úlohy.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 950a786b29a8144c4bb192fa6078e8c88d67481d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384448"
---
# <a name="understand-the-roles-required-to-perform-common-tasks-in-synapse"></a>Pochopení rolí vyžadovaných k provádění běžných úloh v synapse

Tento článek vám pomůže porozumět tomu, které synapse RBAC (řízení přístupu na základě role) nebo role Azure RBAC potřebujete k práci v synapse studiu.  

## <a name="synapse-studio-access-control-and-workflow-summary"></a>Souhrn řízení přístupu synapse Studio a pracovního postupu 

### <a name="accessing-synapse-studio-and-viewing-its-content"></a>Přístup k synapse studiu a zobrazení jejího obsahu

- Můžete otevřít synapse Studio a zobrazit podrobnosti o pracovním prostoru a seznam všech svých prostředků Azure (fondy SQL, fondy Spark nebo prostředí Integration Runtime), pokud jste přiřadili roli synapse RBAC nebo máte roli vlastníka Azure, přispěvatele nebo čtenáře v pracovním prostoru.

### <a name="resource-management"></a>Správa prostředků

- Pokud jste vlastníkem nebo přispěvatelem Azure v pracovním prostoru, můžete vytvořit fondy SQL, fondy Apache Spark a prostředí Integration runtime.
- Vyhrazený fond SQL můžete pozastavit nebo škálovat, nakonfigurovat fond Spark nebo prostředí Integration runtime, pokud jste vlastníkem nebo přispěvatelem Azure v pracovním prostoru nebo v tomto prostředku.

### <a name="viewing-and-editing-code-artifacts"></a>Zobrazení a úpravy artefaktů kódu

- S přístupem k synapse studiu můžete vytvářet nové artefakty kódu, například skripty SQL, poznámkové bloky, úlohy Spark, propojené služby, kanály, datové toky, triggery a přihlašovací údaje.  (Tyto artefakty mohou být publikovány nebo uloženy s dalšími oprávněními.)  
- Pokud jste uživatel artefaktu synapse, vydavatel artefaktu synapse, přispěvatele synapse nebo správce synapse, můžete zobrazit, otevřít a upravit již publikované artefakty kódu.

### <a name="executing-your-code"></a>Provádění kódu

- Pokud máte potřebná oprávnění SQL definovaná v rámci fondů SQL, můžete spouštět skripty SQL v fondech SQL.  
- Můžete spustit poznámkové bloky a úlohy Sparku, pokud máte oprávnění synapse COMPUTE operator pro pracovní prostor nebo konkrétní fondy Apache Spark.  
- Pomocí oprávnění operátora COMPUTE v pracovním prostoru nebo specifických modulech Integration runtime a odpovídajících oprávnění k přihlašovacím údajům můžete spouštět kanály.

### <a name="monitoring-and-managing-execution"></a>Monitorování a Správa provádění
- Pokud jste uživatelem synapse, můžete zkontrolovat stav spuštění poznámkových bloků a úloh v fondech Apache Spark.
- Můžete zkontrolovat protokoly a zrušit spuštěné úlohy a kanály, pokud jste synapse výpočetní operátor v pracovním prostoru nebo pro určitý fond Spark nebo kanál.  

### <a name="publishing-and-saving-your-code"></a>Publikování a ukládání kódu

- Pokud jste Vydavatel artefaktu synapse, synapse přispěvatel nebo správce synapse, můžete do služby publikovat nové nebo aktualizované artefakty kódu. 
- Artefakty kódu můžete potvrdit do pracovní větve úložiště Git, pokud je v pracovním prostoru povolený Git a máte oprávnění Git. Pokud je povolený Git, publikování je povolené jenom z větve pro spolupráci.
- Pokud zavřete synapse Studio bez publikování nebo potvrzení změn artefaktů kódu, tyto změny se ztratí.


## <a name="tasks-and-required-roles"></a>Úkoly a požadované role

V následující tabulce jsou uvedeny běžné úkoly a pro každý úkol, synapse RBAC nebo role Azure RBAC.  

>[!Note]
>- Správce synapse není uveden pro každý úkol, pokud se nejedná o jedinou roli, která poskytuje potřebná oprávnění.  Správce synapse může provádět všechny úlohy povolené jinými rolemi RBAC synapse.</br>
>- Je zobrazena minimální požadovaná role synapse RBAC.
>- Všechny role RBAC synapse v jakémkoli oboru vám poskytnou oprávnění synapse uživatele v pracovním prostoru.
>- Všechna oprávnění a akce synapse RBAC uvedená v tabulce jsou předem opraveny Microsoft/synapse/pracovní prostory/... </br>


Úkol (chci...) |Role (potřebuji...)|Oprávnění/akce synapse RBAC
--|--|--
|Otevření synapse studia v pracovním prostoru|Synapse uživatel nebo|čtení
| |Vlastník, přispěvatel nebo čtenář Azure v pracovním prostoru|žádné
|Výpis fondů SQL, fondů Apache Spark, prostředí Integration runtime a přístup ke svým podrobnostem konfigurace|Synapse uživatel nebo|čtení|
||Vlastník, přispěvatel nebo čtenář Azure v pracovním prostoru|žádné
|Seznam propojených služeb, přihlašovacích údajů, spravovaných privátních koncových bodů|Uživatel synapse|čtení
FONDY SQL|
Vytvoření vyhrazeného fondu SQL nebo fondu SQL bez serveru|Vlastník nebo přispěvatel Azure v pracovním prostoru|žádné
Správa (pozastavení, škálování nebo odstranění) vyhrazeného fondu SQL|Vlastník nebo přispěvatel Azure ve fondu nebo pracovním prostoru SQL|žádné
Vytvoření skriptu SQL</br>|Synapse uživatel nebo </br>Vlastník nebo přispěvatel Azure v pracovním prostoru </br>*Aby bylo možné spustit skript SQL, publikovat nebo potvrdit změny, jsou vyžadovány další oprávnění SQL*.|
Seznam a otevření všech publikovaných skriptů SQL| Uživatel artefaktu synapse, vydavatel artefaktu, přispěvatel synapse|artefakty/čtení
Spuštění skriptu SQL na bez serveru SQL fondu|Oprávnění SQL pro fond (uděleno automaticky správci synapse)|žádné
Spuštění skriptu SQL na vyhrazeném fondu SQL|Oprávnění SQL ve fondu|žádné
Publikování nového, aktualizovaného nebo odstraněného skriptu SQL|Vydavatel artefaktu synapse, přispěvatel synapse|sqlScripts/zapisovat, odstranit
Potvrďte změny ve skriptu SQL do úložiště Git.|Vyžaduje oprávnění Git v úložišti.|
Přiřazení Správce služby Active Directory v pracovním prostoru (prostřednictvím vlastností pracovního prostoru na webu Azure Portal)|Vlastník nebo přispěvatel Azure v pracovním prostoru |
FONDY APACHE SPARK|
Vytvoření fondu Apache Spark|Vlastník nebo přispěvatel Azure v pracovním prostoru|
Monitorování Apache Sparkch aplikací| Uživatel synapse|čtení
Zobrazení protokolů pro Poznámkový blok a provádění úloh |Operátor COMPUTE synapse|
Zrušení všech poznámkových bloků nebo úloh Sparku spuštěných ve fondu Apache Spark|Synapse výpočetní operátor na fondu Apache Spark.|bigDataPools/useCompute
Vytvoření definice poznámkového bloku nebo úlohy|Synapse uživatel nebo </br>Vlastník, přispěvatel nebo čtenář Azure v pracovním prostoru</br> *Aby bylo možné spustit, publikovat nebo potvrdit změny, jsou vyžadovány další oprávnění.*|čtení</br></br></br></br></br> 
Seznam a otevření publikovaného poznámkového bloku nebo definice úlohy včetně kontroly uložených výstupů|Uživatel artefaktu synapse, vydavatel artefaktu synapse, přispěvatel synapse v pracovním prostoru|artefakty/čtení
Spustit Poznámkový blok a zkontrolovat jeho výstup nebo odeslat Sparkovou úlohu|Synapse Apache Spark Administrator, operátor synapse COMPUTE na vybraném fondu Apache Spark|bigDataPools/useCompute 
Publikování nebo odstranění definice poznámkového bloku nebo úlohy (včetně výstupu) do služby|Vydavatel artefaktů v pracovním prostoru, synapse Apache Spark správce|poznámkové bloky/zápis, odstranění
Potvrďte změny v poznámkovém bloku nebo definici úlohy do úložiště Git.|Oprávnění Gitu|žádné
KANÁLY, MODULY RUNTIME INTEGRACE, TOKY DAT, DATOVÉ SADY & TRIGGERY|
Vytvoření, aktualizace nebo odstranění prostředí Integration runtime|Vlastník nebo přispěvatel Azure v pracovním prostoru|
Monitorování stavu modulu runtime integrace|Uživatel synapse|čtení, kanály/viewOutputs
Kontrola spuštění kanálu|Přispěvatel/synapse artefaktu synapse|čtení, kanály/viewOutputs 
Vytvoření kanálu |Uživatel synapse</br>*Pro ladění, přidávání triggerů, publikování nebo potvrzení změn se vyžadují další oprávnění synapse.*|čtení
Vytvoření datového toku nebo datové sady |Uživatel synapse</br>*K publikování nebo potvrzení změn se vyžadují další oprávnění synapse.*|čtení
Vypsání a otevření publikovaného kanálu |Uživatel artefaktu synapse | artefakty/čtení
Náhled dat datové sady|Uživatel s přihlašovacími údaji uživatele synapse + synapse na WorkspaceSystemIdentity| 
Ladění kanálu pomocí výchozího prostředí Integration runtime|Synapse uživatele a synapse přihlašovací údaje uživatele k přihlašovacím údajům WorkspaceSystemIdentity|oprávnění </br>pověření/useSecret
Vytvoření triggeru, včetně aktivace nyní (vyžaduje oprávnění ke spuštění kanálu)|Uživatel s přihlašovacími údaji uživatele synapse + synapse na WorkspaceSystemIdentity|čtení, pověření/useSecret/akce
Spuštění nebo spuštění kanálu|Uživatel s přihlašovacími údaji uživatele synapse + synapse na WorkspaceSystemIdentity|čtení, pověření/useSecret/akce
Kopírování dat pomocí nástroje Kopírování dat|Synapse uživatele + synapse přihlašovací údaje uživatele k identitě systému pracovního prostoru|čtení, pověření/useSecret/akce
Ingestování dat (pomocí plánu)|Synapse Author + synapse – uživatel pověření na identitě systému pracovního prostoru|čtení, pověření/useSecret/akce
Publikování nového, aktualizovaného nebo odstraněného kanálu, toku dat nebo triggeru do služby|Vydavatel artefaktu synapse v pracovním prostoru|kanály/zapisovat, odstraňovat</br>tok dat/zápis, odstranění</br>triggery/zápis, odstranění
Potvrzení změn u kanálů, datových toků, datových sad nebo triggerů do úložiště Git |Oprávnění Gitu|žádné 
PROPOJENÉ SLUŽBY|
Vytvoření propojené služby (včetně přiřazování přihlašovacích údajů)|Uživatel synapse</br>*K použití propojené služby s přihlašovacími údaji nebo k publikování nebo potvrzení změn se vyžaduje další oprávnění.*|čtení
Seznam a otevření publikované propojené služby|Uživatel artefaktu synapse|linkedServices/zapisovat, odstranit  
Testování připojení u propojené služby zabezpečené pomocí pověření|Uživatel s přihlašovacími údaji uživatele synapse + synapse|pověření/useSecret/akce|
Publikování propojené služby|Vydavatel artefaktu synapse, propojený Data Manager synapse|linkedServices/zapisovat, odstranit
Potvrzení definic propojených služeb do úložiště Git|Oprávnění Gitu|žádné
SPRÁVA PŘÍSTUPU|
Kontrola přiřazení rolí RBAC synapse v jakémkoli oboru|Uživatel synapse|čtení
Přiřazení a odebrání přiřazení rolí synapse RBAC pro uživatele, skupiny a instanční objekty| Správce synapse v pracovním prostoru nebo v určitém oboru položky pracovního prostoru|roleAssignments/zapisovat, odstranit 

>[!Note]
>Uživatelé typu host z jiného tenanta nemůžou zkontrolovat, přidat ani změnit přiřazení rolí bez ohledu na to, jakou roli jste přiřadili. 

## <a name="next-steps"></a>Další kroky

Přečtěte [si, jak zkontrolovat přiřazení rolí RBAC synapse](./how-to-review-synapse-rbac-role-assignments.md)

Naučte [se spravovat přiřazení rolí RBAC synapse](./how-to-manage-synapse-rbac-role-assignments.md). 

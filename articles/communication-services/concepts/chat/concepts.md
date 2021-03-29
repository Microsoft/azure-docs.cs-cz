---
title: Koncepce chatu v komunikačních službách Azure
titleSuffix: An Azure Communication Services concept document
description: Přečtěte si o konceptech chatu komunikačních služeb.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: cf500d529eb22cdd333d796f156eedcd284ea20d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642306"
---
# <a name="chat-concepts"></a>Koncepce chatu 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Sady SDK pro chat pro komunikaci v reálném čase můžete použít k přidání textového chatu do vašich aplikací. Tato stránka shrnuje klíčové pojmy a možnosti chatu.    

Další informace o konkrétních jazycích a funkcích sady SDK najdete v tématu [Přehled sady Communications Chat SDK](./sdk-features.md) .  

## <a name="chat-overview"></a>Přehled chatu    

Chatové konverzace se vyskytují v rámci **konverzačních vláken**. Vlákna chatu mají následující vlastnosti:

- Vlákno chatu je jedinečným způsobem identifikováno `ChatThreadId` . 
- Vlákna chatu můžou mít jednoho nebo více uživatelů, kteří jim můžou poslat zprávy. 
- Uživatel může být součástí jednoho nebo více vláken chatu. 
- Pouze účastníci vlákna mají přístup k danému vláknu chatu a mohou provádět pouze operace s vlákny chatu. Mezi tyto operace patří odesílání a příjem zpráv, přidávání účastníků a odebírání účastníků. 
- Uživatelé jsou automaticky přidáni jako účastník na všechna zřetězená vlákna, která vytvoří.

### <a name="user-access"></a>Přístup uživatelů
Tvůrce vlákna a účastníci obvykle mají stejnou úroveň přístupu ke vláknu a můžou spouštět všechny související operace, které jsou v sadě SDK dostupné, včetně jejich odstranění. Účastníci nemají oprávnění k zápisu zpráv odesílaných jinými účastníky, což znamená, že odeslané zprávy může aktualizovat nebo odstranit pouze odesílatel zprávy. Pokud se k tomu pokusí další účastník, zobrazí se chyba. 

Pokud chcete omezit přístup k funkcím chatu pro skupinu uživatelů, můžete nakonfigurovat přístup jako součást vaší důvěryhodné služby. Vaše důvěryhodná služba je služba, která orchestruje ověřování a autorizaci účastníků chatu. Podrobněji to prozkoumáme níže.  

### <a name="chat-data"></a>Data chatu 
Komunikační služby ukládají historii chatu, dokud je explicitně neodstraníte. Účastníci vlákna konverzace můžou použít `ListMessages` k zobrazení historie zpráv pro konkrétní vlákno. Uživatelé odebrání z konverzačního vlákna budou moci zobrazit předchozí historii zpráv, ale nebudou moci odesílat ani přijímat nové zprávy jako součást tohoto konverzačního vlákna. Plně nečinné vlákno bez účastníků se po 30 dnech automaticky odstraní. Další informace o datech uložených komunikačními službami najdete v dokumentaci o [ochraně osobních údajů](../privacy.md).  

### <a name="service-limits"></a>Omezení služby  
- Maximální počet účastníků povolených ve vlákně chatu je 250.   
- Maximální povolená velikost zprávy je přibližně 28 KB.  
- Pro vlákna chatu s více než 20 účastníky se příjem čtení a zadávání funkcí indikátoru nepodporuje.    

## <a name="chat-architecture"></a>Architektura chatu    

K dispozici jsou dvě základní součásti pro konverzaci: 1) klientská aplikace pro důvěryhodné služby a 2).    

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagram znázorňující architekturu chatu komunikačních služeb"::: 

 - **Důvěryhodná služba:** Aby bylo možné správně spravovat relaci konverzace, potřebujete službu, která vám pomůže se připojit ke komunikačním službám pomocí připojovacího řetězce prostředků. Tato služba zodpovídá za vytváření chatovacích vláken, přidávání a odebírání účastníků a vydávání přístupových tokenů uživatelům. Další informace o přístupových tokenech najdete v našem rychlém startu [přístupových tokenů](../../quickstarts/access-tokens.md) .  
 - **Klientská aplikace:**  Klientská aplikace se připojí k vaší důvěryhodné službě a získá přístupové tokeny, které používají uživatelé pro připojení přímo ke komunikačním službám. Jakmile vaše důvěryhodná služba vytvoří chatovací vlákno a přidají uživatele jako účastníky, může pomocí klientské aplikace připojit se k konverzačnímu vláknu a odesílat zprávy. Použijte funkci oznámení v reálném čase, kterou budeme podrobněji popsáni v klientské aplikaci, abyste se mohli přihlásit k odběru zprávy &ch vláken od jiných účastníků.
    
        
## <a name="message-types"></a>Typy zpráv    

V rámci historie zpráv chat sdílí uživatelem generované zprávy i zprávy generované systémem. Systémové zprávy jsou generovány při aktualizaci vlákna chatu a mohou poznat, kdy byl účastník přidán nebo odebrán nebo když bylo aktualizováno téma konverzačního vlákna. Při volání `List Messages` nebo `Get Messages` v konverzačním vlákně bude výsledek obsahovat jak typ zprávy, tak v chronologickém pořadí.

U uživatelem generovaných zpráv je možné typ zprávy nastavit `SendMessageOptions` při odesílání zprávy do konverzačního vlákna. Pokud není zadána žádná hodnota, budou ve výchozím nastavení služby Communications `text` Type. Nastavení této hodnoty je důležité při posílání kódu HTML. Když `html` se zadá, komunikační služby upraví obsah, aby se zajistilo, že se na klientských zařízeních bezpečně vykreslí.
 - `text`: Zpráva s prostým textem složená a odeslaná uživatelem jako součást konverzačního vlákna. 
 - `html`: Formátovaná zpráva pomocí HTML, která se skládá a odesílá uživatelem jako součást konverzačního vlákna. 

Typy systémových zpráv: 
 - `participantAdded`: Systémová zpráva, která indikuje, že jeden nebo více účastníků bylo přidáno do konverzačního vlákna.
 - `participantRemoved`: Systémová zpráva, která indikuje, že účastník byl odebrán z konverzačního vlákna.
 - `topicUpdated`: Systémová zpráva, která indikuje, že téma vlákna bylo aktualizováno.

## <a name="real-time-notifications"></a>Oznámení v reálném čase  

Některé sady SDK (například JavaScript Chat SDK) podporují oznámení v reálném čase. Tato funkce umožňuje klientům naslouchat komunikačním službám pro aktualizace v reálném čase a příchozí zprávy do konverzačního vlákna, aniž by museli dotazovat rozhraní API. Klientská aplikace se může přihlásit k odběru následujících událostí:
 - `chatMessageReceived` – Když účastník odešle novou zprávu do konverzačního vlákna.
 - `chatMessageEdited` – Při úpravě zprávy ve vlákně chatu. 
 - `chatMessageDeleted` – Při odstranění zprávy ve vlákně chatu.   
 - `typingIndicatorReceived` – když jiný účastník pošle do konverzačního vlákna indikátor zápisu.    
 - `readReceiptReceived` – když jiný účastník pošle oznámení o přečtení pro zprávu, kterou si přečte.  
 - `chatThreadCreated` – Když uživatel komunikačních služeb vytvoří vlákno konverzace.    
 - `chatThreadDeleted` – Když uživatel komunikačních služeb odstraní vlákno konverzace.    
 - `chatThreadPropertiesUpdated` – Když se aktualizují vlastnosti vlákna chatu; v současné době je podporována pouze aktualizace tématu pro vlákno. 
 - `participantsAdded` – Když se uživatel přidá jako účastník konverzačního vlákna.     
 - `participantsRemoved` – Při odebrání existujícího účastníka z konverzačního vlákna.

Oznámení v reálném čase je možné využít k poskytování možností chatování v reálném čase pro vaše uživatele. Aby bylo možné odesílat nabízená oznámení pro zprávy, které nezmizí vaši uživatelé, zatímco byly pryč, komunikační služby se integrují s Azure Event Grid pro publikování událostí souvisejících s chatem (operace post), které se dají připojit do vlastní služby oznámení aplikací. Další podrobnosti najdete v tématu [události serveru](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fcommunication-services%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json).


## <a name="build-intelligent-ai-powered-chat-experiences"></a>Vytváření inteligentních prostředí pro konverzaci s AI   

Pomocí [rozhraní API pro rozpoznávání Azure](../../../cognitive-services/index.yml) se sadou Chat SDK můžete vytvářet případy použití jako:

- Umožněte uživatelům, aby v různých jazycích navzájemly konverzaci.  
- Pomoc agentovi podpory určení priorit lístků zjištěním záporné mínění příchozí zprávy od zákazníka. 
- Analyzujte příchozí zprávy pro detekci klíčů a rozpoznávání entit a pro uživatele v aplikaci podle obsahu zprávy vyzvat relevantní informace.

Jedním ze způsobů, jak toho dosáhnout, je použít vaši důvěryhodnou službu jako účastníka konverzačního vlákna. Řekněme, že chcete povolit převod jazyka. Tato služba bude odpovědná za naslouchání zpráv vyměňovaných jinými účastníky [1], volání rozhraní API pro rozpoznávání obsahu pro překlad obsahu na požadovaný jazyk [2, 3] a odeslání přeloženého výsledku jako zprávy ve vlákně chatu [4].

Historie zpráv tak bude obsahovat původní i přeložené zprávy. V klientské aplikaci můžete přidat logiku pro zobrazení původní nebo přeložené zprávy. V [tomto rychlém](../../../cognitive-services/translator/quickstart-translator.md) startu se naučíte, jak používat rozpoznávání rozhraní API k překladu textu do různých jazyků. 
    
:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagram znázorňující Cognitive Services interakci s komunikačními službami."::: 

## <a name="next-steps"></a>Další kroky   

> [!div class="nextstepaction"] 
> [Začínáme s chatem](../../quickstarts/chat/get-started.md)    

Následující dokumenty můžou být zajímavé:  
- Seznámení se sadou [Chat SDK](sdk-features.md)

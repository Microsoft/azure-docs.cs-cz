Teď, když přidáte podmínku s daty, který je generován aktivační událost zajímavé své doby něco udělat. Postupujte podle těchto kroků přidejte **Salesforce – Get object** akce. Tato akce budou získávat data pokaždé, když se vytvoří nový zájemce. Pokud přidáte druhou akci, která použije data ze služby Salesforce – Get object akci k odesílání e-mailu pomocí konektoru Office 365.  

Ke konfiguraci této akce, budete muset zadat následující informace. Uvidíte, že je snadno použitelný dat vygenerovaných vašimi aktivační událost jako vstup pro některé vlastnosti nového souboru:

| Vytvoření vlastnosti souboru | Popis |
| --- | --- |
| Typ objektu |Toto je typ objektu Salesforce, které vás zajímají. Příklady jsou potenciálních zákazníků, účet, atd. |
| ID objektu |Reprezentuje identifikátor pro objekt. |

1. Vyberte **přidat akci** odkaz. Tato otevře vyhledávací pole, kde můžete vyhledat jakoukoli akci jste chtěli provést. V tomto příkladu Salesforce akce jsou zajímavé.      
   ![Obrázek akce Salesforce 1](./media/connectors-create-api-salesforce/action-1.png)  
2. Zadejte *salesforce* hledání pro akce související s salesforce.
3. Vyberte **Salesforce – Get object** jako akce má být provedena.   **Poznámka:**: zobrazí výzva k autorizaci aplikace logiky přístup ke svému účtu Salesforce, pokud jste tak dosud neučinili.    
   ![Obrázek akce Salesforce 2](./media/connectors-create-api-salesforce/action-2.png)    
4. **Get object** ovládací prvek se otevře.  
5. Vyberte *vést* jako typ objektu.
6. Vyberte **ID objektu** ovládacího prvku.
7. Vyberte **...**  rozbalte seznam tokenů, které lze použít jako vstup pro akce.       
   ![Obrázek akce Salesforce 3](./media/connectors-create-api-salesforce/action-3.png)    
8. Vyberte **vést ID** ovládací prvek se otevře.   
   ![Obrázek akce Salesforce 4](./media/connectors-create-api-salesforce/action-4.png)     
9. Všimněte si, že vedoucí ID tokenu je teď v ovládacím prvku ID objektu udávající, že akce Get object bude hledat potenciálních zákazníků s ID, který je roven ID potenciálního zákazníka z potenciálních zákazníků, který aktivuje tuto aplikaci logiky.  
   ![Obrázek akce Salesforce 5](./media/connectors-create-api-salesforce/action-5.png)  
10. Uložte svou práci. To je vše, akce Get object jste přidali do aplikace logiky. Ovládací prvek objektu Get by měl vypadat nějak takto:    
    ![Obrázek akce Salesforce 6](./media/connectors-create-api-salesforce/action-6.png)  

Teď, když přidáte akci, kterou chcete získat nový zájemce, můžete chtít Udělejte něco zajímavého. u nově vytvořeného zájemce. V podniku můžete chtít odeslat e-mail s oznámením distribuční seznam, vytvoření nového zájemce. S použitím konektoru Office 365 odeslat e-mail s některými z příslušné informace z objektu nové zájemce v Salesforce.  

1. Vyberte **přidat akci** zadejte *e-mailu* v ovládacím prvku hledání. Tím se vyfiltrují akce na ty, které se vztahují k odesílání a příjem e-mailu.  
2. Vyberte **Office 365 Outlook – odeslat e-mail** položky seznamu. Pokud jste ještě nevytvořili *připojení* ke svému účtu Office 365, zobrazí výzva k zadání přihlašovacích údajů Office 365 k jeho vytvoření. Jakmile budete hotovi, **odeslat e-mailu** ovládací prvek se otevře.        
   ![Obrázek akce Salesforce 7](./media/connectors-create-api-salesforce/action-7.png)  
3. Zadejte e-mailovou adresu, která chcete k odeslání v e-mailu **k** ovládacího prvku.
4. V **subjektu** řídit, zadejte *vytvoří nový zájemce* – vyberte *společnosti* token. Bude se zobrazovat *společnosti* pole z nového zájemce v Salesforce se vytvoří.  
5. V **tělo** ovládací prvek, vyberte některou z tokenů z nové zájemce objektu a můžete také zadat jakýkoli text, který se má zobrazit v textu e-mailu. Tady je příklad:  
   ![Obrázek akce Salesforce 8](./media/connectors-create-api-salesforce/action-8.png)   
6. Uložení pracovního postupu.  

A je to. Aplikace logiky je nyní dokončena.  

Teď můžete otestovat svou aplikaci logiky: v Salesforce, vytvoří se nový zájemce, který splňuje podmínky, které jste vytvořili.  Pokud jste plně postupovali podle tohoto návodu, stačí vytvořit nový zájemce s e-mailovou adresu, která obsahuje *amazon.com* v ní. Po pár sekundách by měly aktivovat svou aplikaci logiky a výsledky mohou vypadat nějak takto:  
![Obrázek akce Salesforce 9](./media/connectors-create-api-salesforce/action-9.png)  


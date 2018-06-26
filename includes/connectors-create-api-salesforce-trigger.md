V tohoto návodu se dozvíte, jak používat **Salesforce – když je vytvořen objekt** aktivační událost zahájíte pracovní postup aplikace logiky po nové zájemce ve vaší služby Salesforce.

> [!NOTE]
> Získat zobrazí výzva k přihlášení ke svému účtu Salesforce, pokud jste ještě nevytvořili *připojení* do služby Salesforce.  
> 
> 

1. Zadejte *salesforce* do vyhledávacího pole v designeru aplikace logiky zvolte **Salesforce – když je vytvořen objekt** aktivační události.  
   ![Obrázek aktivační události služby Salesforce 1](./media/connectors-create-api-salesforce/trigger-1.png)   
2. **Když je vytvořen objekt** ovládací prvek je zobrazen.  
   ![Obrázek aktivační události služby Salesforce 2](./media/connectors-create-api-salesforce/trigger-2.png)   
3. Vyberte **typ objektu** vyberte *vést* ze seznamu objektů. V tomto kroku jsou indikující, že vytváříte aktivační událost, která vás upozorní aplikace logiky, vždy, když se vytvoří nové zájemce v Salesforce.   
   ![Obrázek aktivační události služby Salesforce 3](./media/connectors-create-api-salesforce/trigger-3.png)   
4. A je to. Vytvoření aktivační událost. Však musíte vytvořit alespoň jednu akci. Chcete-li vytvořit platný logiku aplikace.    
   ![Obrázek aktivační události služby Salesforce 4](./media/connectors-create-api-salesforce/trigger-4.png)   

Aplikace logiky v tomto okamžiku je nakonfigurovaná s aktivační událost, která zahájí spuštění ostatních triggery a akce v pracovním postupu při vytvoření nové položky ve vaší služby Salesforce.  


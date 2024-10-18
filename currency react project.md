### **1\. Project Overview (Currency Converter Application)**

Yeh project ek **currency converter app** hai jo **React.js** ka use karke banaya gaya hai. Yeh app users ko allow karta hai:

* Do currencies select karna: "From" (se) aur "To" (tak).  
* "From" currency mein ek amount daalna.  
* Real-time exchange rates ke zariye us amount ko "To" currency mein convert karna.  
* "From" aur "To" currency ko swap karna.

Is project ke main 3 files hain:

* **App.jsx**  
* **InputBox.jsx**  
* **useCurrencyInfo.js**

---

### **2\. Project Structure**

Files ka structure aur unka role yeh hai:

* **App.jsx**: Yeh main component hai, jisme sab kuch handle hota hai: user inputs, form submit karna, aur input fields dikhana.  
* **InputBox.jsx**: Ek reusable component hai jo amount aur currency select karne ke liye input field aur dropdown ko manage karta hai.  
* **useCurrencyInfo.js**: Custom React hook hai jo real-time exchange rates ko API se fetch karta hai.

---

### **3\. Line-by-Line Breakdown of Each Component**

#### **Step 1: App Component (State Management)**

App component ke andar hum React ka **useState** hook use kar rahe hain taaki alag-alag data ko manage kar sakein.

js  
Copy code  
`const [amount, setAmount] = useState(0);`   
`const [from, setFrom] = useState("usd");`  
`const [to, setTo] = useState("inr");`  
`const [convertedAmount, setConvertedAmount] = useState(0);`

* **useState Hook**:  
  * `useState` ek **React hook** hai jo functional components mein state ko handle karta hai.  
  * `amount`: User jo value convert karna chahta hai, woh yahan store hoti hai.  
  * `from`: "From" currency ka code (default `"usd"`).  
  * `to`: "To" currency ka code (default `"inr"`).  
  * `convertedAmount`: Yeh conversion ke baad ki value store karta hai.

Jab user currency ya amount change karta hai, toh yeh state values real-time mein update hoti hain.

---

#### **Step 2: useCurrencyInfo.js (Fetching Data from API)**

Yeh file ek custom hook hai jo real-time currency data fetch karta hai.

js  
Copy code  
`useEffect(() => {`  
    ``fetch(`https://cdn.jsdelivr.net/npm/@fawazahmed0/currency-api@latest/v1/currencies/${currency}.json`)``  
        `.then((res) => res.json())`  
        `.then((res) => setData(res[currency]));`  
`}, [currency]);`

* **useEffect Hook**:  
  * `useEffect` ek side effect ko perform karta hai, jaise ki data fetch karna. Jab bhi `currency` change hoti hai, yeh hook API se latest exchange rate fetch karta hai.  
  * `fetch` API call use karke external API se data liya jaata hai, jo dynamically `currency` ke hisaab se hota hai.  
* **Response Handling**:  
  * JSON format mein response aata hai aur uske andar exchange rates hoti hain, jo `currencyInfo` state mein store ki jaati hain.

Agar `from` currency `"usd"` hai, toh `currencyInfo` kuch aisa dikhega:

js  
Copy code  
`{`  
  `"usd": {`  
    `"eur": 0.84,`  
    `"inr": 74.54,`  
    `"jpy": 110.93,`  
    `// aur bhi rates...`  
  `}`  
`}`

---

#### **Step 3: InputBox.jsx (Reusable Input Component)**

Yeh component user se amount input karwane aur currency dropdown dikhane ka kaam karta hai.

jsx  
Copy code  
`function InputBox({`  
    `label,`  
    `amount,`  
    `onAmountChange,`  
    `onCurrencyChange,`  
    `currencyOptions,`  
    `selectCurrency,`  
    `amountDisable,`  
    `currencyDisable`  
`}) {`  
    `return (`  
        `<div className="input-container">`  
            `<input`  
                `type="number"`  
                `value={amount}`  
                `onChange={(e) => onAmountChange(Number(e.target.value))}`  
                `disabled={amountDisable}`  
            `/>`  
            `<select`  
                `value={selectCurrency}`  
                `onChange={(e) => onCurrencyChange(e.target.value)}`  
                `disabled={currencyDisable}`  
            `>`  
                `{currencyOptions.map((currency) => (`  
                    `<option key={currency} value={currency}>`  
                        `{currency}`  
                    `</option>`  
                `))}`  
            `</select>`  
        `</div>`  
    `);`  
`}`

* **Props**:  
  * `label`: Bataata hai ki yeh input box "From" ke liye hai ya "To" ke liye.  
  * `amount`: Input field ka value hota hai, jo user daalta hai.  
  * `onAmountChange`: Jab user amount change karta hai, toh yeh function call hota hai.  
  * `onCurrencyChange`: Currency change hone par yeh function call hota hai.  
  * `currencyOptions`: Yeh array of currencies ko dropdown mein dikhata hai.

---

#### **Step 4: Conversion Logic in App.jsx**

Conversion ka logic yeh function handle karta hai:

js  
Copy code  
`const convert = () => {`  
  `if (currencyInfo && currencyInfo[to]) {`  
    `setConvertedAmount(amount * currencyInfo[to]);`  
  `} else {`  
    `alert("Conversion rate not available.");`  
  `}`  
`};`

* **convert Function**:  
  * Yeh check karta hai ki target "To" currency ka exchange rate `currencyInfo` mein available hai ya nahi.  
  * Agar available ho, toh entered `amount` ko exchange rate se multiply karke result `convertedAmount` mein store karta hai.  
  * Agar data nahi mile, toh ek alert show karta hai.

---

#### **Step 5: Swap Functionality in App.jsx**

Swap ka kaam currencies ko interchange karna hai:

js  
Copy code  
`const swap = () => {`  
  `const temp = from;`  
  `setFrom(to);`  
  `setTo(temp);`  
  `if (currencyInfo && currencyInfo[to]) {`  
    `setConvertedAmount(amount * currencyInfo[to]);`  
  `}`  
`};`

* **swap Function**:  
  * `from` currency ko temporary variable `temp` mein store karke `from` aur `to` ko swap karta hai.  
  * Swap ke baad conversion dobara calculate karta hai.

---

#### **Step 6: Handling Form Submission**

Form submit hone par yeh code execute hota hai:

jsx  
Copy code  
`<form onSubmit={(e) => {`  
  `e.preventDefault();`  
  `convert();`  
`}}>`

* **Form Handling**:  
  * Default form submit hone ki behavior ko prevent karta hai taaki page reload na ho.  
  * Instead, convert function call hota hai aur conversion result show karta hai.

---

#### **Step 7: Rendering the UI**

App ka UI kuch aisa dikh raha hai:

jsx  
Copy code  
`<div className="w-full h-screen flex justify-center items-center">`  
  `<div className="w-full max-w-md mx-auto">`  
    `<form onSubmit={convert}>`  
      `<InputBox`  
        `label="From"`  
        `amount={amount}`  
        `currencyOptions={options}`  
        `onCurrencyChange={setFrom}`  
        `selectCurrency={from}`  
        `onAmountChange={setAmount}`  
      `/>`  
      `<button onClick={swap}>Swap</button>`  
      `<InputBox`  
        `label="To"`  
        `amount={convertedAmount}`  
        `currencyOptions={options}`  
        `onCurrencyChange={setTo}`  
        `selectCurrency={to}`  
        `amountDisable`  
      `/>`  
      `<button type="submit">Convert</button>`  
    `</form>`  
  `</div>`  
`</div>`

* **Layout**:  
  * Tailwind CSS classes use karke is layout ko style kiya gaya hai. Yeh form screen ke center mein dikhaya gaya hai.  
  * Form mein do `InputBox` components hain — ek "From" currency ke liye aur ek "To" currency ke liye.  
  * Ek **Swap** button hai jo currencies ko swap karta hai aur ek **Convert** button jo conversion ko trigger karta hai.

---

### **4\. How Everything Works Together**

1. **Initial Load**:  
   * App default "From" currency `"usd"` aur "To" currency `"inr"` ke saath start hota hai. User ko amount enter karna hota hai.  
2. **User Interaction**:  
   * User currency select karta hai aur amount enter karta hai. Jab **Convert** button dabaya jaata hai, toh app relevant exchange rate ko API se fetch karta hai aur converted amount calculate karta hai.  
3. **Conversion Result**:  
   * Conversion ka result "To" box mein dikhaya jaata hai. Agar exchange rate unavailable ho, toh ek alert show hota hai.  
4. **Swap Functionality**:  
   * Swap button ko click karke user "From" aur "To" currencies ko switch kar sakta hai, jisse conversion nayi selection ke hisaab se hoti hai.

---

### **5\. Additional Features & Improvements**

* **Error Handling**: Network ya API failure ke liye better error handling daali jaa sakti hai.  
* **Input Validation**: Amount field mein valid input ke liye validation add ki jaa sakti hai.  
* **Styling**: User interface ko aur improve karne ke liye additional styling ki jaa sakti hai.  
* **Loading Indicators**: Jab tak data API se fetch ho raha ho, tab tak loading indicators show kiye jaa sakte hain.


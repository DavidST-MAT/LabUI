<template>
    <div class="modal">
      <div class="modal-content flex flex-col items-center">
        <h2 class="sample-number-heading">Sample number: {{ sampleNumber }}</h2>
        <div class="header flex">
          <NewButton @newButtonClick="handleNewButtonClick" />
          <button type="button" @click="handleOpenButtonClick" class="hidden-print mt-4  text-gray-900 hover:text-white border border-gray-800 hover:bg-gray-900 focus:ring-4 focus:outline-none focus:ring-gray-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center my-2 mr-2 dark:border-gray-600 dark:text-gray-400 dark:hover:text-white dark:hover:bg-gray-600 dark:focus:ring-gray-800">Open</button>
        </div>

          <template v-if="isNewButtonClicked || isOpenButtonClicked">
      
            <table  class="mx-auto my-4 border-separate border-spacing-2 border border-slate-500 text-sm text-left dark:bg-gray-100 text-gray-500 dark:text-gray-100 mt-100">
              <tbody id="labUIdata">
                <tr class="bg-white border-b dark:bg-gray-200 dark:border-gray-300" v-for="(item, index) in this.headerData" :key="index">
                  <th scope="row" class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap dark:text-black">
                    {{ item.Parameter }}
                  </th>
                  <td class="px-6 py-4 dark:text-black">
                    <template v-if="item.Parameter === 'Tester'">
                      <TesterInput @input-change="handleInputChange" />
                    </template>
                    <template v-else-if="item.Parameter === 'Article'">
                      <ArticleInput @input-change="handleInputChange"/>
                    </template>
                    <template v-else-if="item.Parameter === 'Order number'">
                      <OrderInput @input-change="handleInputChange"/>
                    </template>
                    <template v-else-if="item.Parameter === 'Batch number'">
                      <BatchInput @input-change="handleInputChange"/>
                    </template>
                    <template v-else-if="item.Parameter === 'Article number'">
                      <ArticleNumberInput @input-change="handleInputChange"/>
                    </template>
                    <template v-else>
                      <input v-model="item.Value" type="text" />
                    </template>
                  </td>

                </tr>
              </tbody>    
            </table>

            <table class="mx-auto my-4 border-separate border-spacing-2 border border-slate-500 text-sm text-left dark:bg-gray-100 text-gray-500 dark:text-gray-100 mt-100">
              <tbody>
                <tr class="bg-white border-b dark:bg-gray-200 dark:border-gray-300" v-for="(item, index) in this.labData" :key="index">
                  <th scope="row" class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap dark:text-black">
                    {{ item.Parameter }}
                  </th>
                  <td class="px-6 py-4 dark:text-black">
                    <input v-model="item.Value" type="text"/>
                  </td>
                  <td class="px-6 py-4 dark:text-black">
                    {{ item.Unit }}
                  </td>
                  <td class="px-6 py-4 dark:text-black">
                    <SetButton :parameter="item.Parameter" :labValue="item.Value"/>
                    <!-- <button type="button" @click="handleSetButtonClick(item)" class="btn hidden-print">Set</button> -->
                  </td>
                </tr>
              </tbody>    
            </table>

            <div class="flex">
              <!-- <button type="button" @click="" class="hidden-print text-gray-900 hover:text-white border border-gray-800 hover:bg-gray-900 focus:ring-4 focus:outline-none focus:ring-gray-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center my-2 mr-2 dark:border-gray-600 dark:text-gray-400 dark:hover:text-white dark:hover:bg-gray-600 dark:focus:ring-gray-800">Save</button> -->
              <SaveButton :headerData="headerData" :sampleNumber="sampleNumber" :labData="labData" @button-clicked="handleSaveButtonClick"/>
              <PrintButton/>
            </div>

          </template>

     </div>
    </div>
  </template>
  
  <script>
  import NewButton from './NewButton.vue';
  import PrintButton from './PrintButton.vue';
  import ArticleInput from './ArticleInput.vue';
  import TesterInput from './TesterInput.vue';
  import BatchInput from './BatchInput.vue';
  import OrderInput from './OrderInput.vue';
  import ArticleNumberInput from './ArticleNumberInput.vue';
  import SaveButton from './SaveButton.vue';
  import SetButton from './SetButton.vue';


  export default {
    props: {
      show: Boolean,
    },
  
    data() {
      return {
        isNewButtonClicked: false,
        isOpenButtonClicked: false,
        sampleNumber: '',
        currentValue: '',
        testerName: '',
        suggest: '',
        labValue: '',
        headerData: [
          { Parameter: 'Probe Date/Time', Value: '' },
          { Parameter: 'Tester', Value: '' },
          { Parameter: 'Test', Value: '' },
          { Parameter: 'Test standard', Value: '' },
          { Parameter: 'Article', Value: '' },
          { Parameter: 'Article number', Value: '' },
          { Parameter: 'Order number', Value: '' },
          { Parameter: 'Batch number', Value: '' },
          { Parameter: 'Comment', Value: '' },
        ],
        labData: [
          { Parameter: 'maximum tensile force 1', Value: '', Unit: 'N' },
          { Parameter: 'maximum tensile force 2', Value: '', Unit: 'N' },
          { Parameter: 'maximum tensile force 3', Value: '', Unit: 'N' },
          { Parameter: 'maximum tensile force 4', Value: '', Unit: 'N' },
          { Parameter: 'maximum tensile force 5', Value: '', Unit: 'N' },
          { Parameter: 'maximum stretch 1', Value: '', Unit: '%' },
          { Parameter: 'maximum stretch 2', Value: '', Unit: '%' },
          { Parameter: 'maximum stretch 3', Value: '', Unit: '%' },
          { Parameter: 'maximum stretch 4', Value: '', Unit: '%' },
          { Parameter: 'maximum stretch 5', Value: '', Unit: '%' },
        ],
      };
    },
  
    methods: {

      handleInputChange(paramterName, value) {
        this.headerData.find(item => item.Parameter === paramterName).Value = value;
      },

      handleOpenButtonClick() {
        this.isOpenButtonClicked = true;
      },

      // Function for clicking button "New"
      handleNewButtonClick(result) {
        this.isNewButtonClicked = true;
        const currentDate = new Date();
        const formattedDate = currentDate.toLocaleString(); // You can customize the date format as needed
  
        // Find the "Probe Date/Time" item in the data array and update its value
        const probeDateTimeItem = this.headerData.find(item => item.Parameter === 'Probe Date/Time');
        if (probeDateTimeItem) {
          probeDateTimeItem.Value = formattedDate;
        }

        // Increment the sample number
        this.sampleNumber = result
        this.sampleNumber++
        console.log(this.sampleNumber)
      },


      handleSaveButtonClick() {
        this.isNewButtonClicked = false;
        this.isOpenButtonClicked = false;
        console.log("isNewButtonClicked:", this.isNewButtonClicked);
        console.log("isOpenButtonClicked:", this.isOpenButtonClicked);
      },
  
      // Add other methods as needed
    },

    components: {
      NewButton,
      PrintButton,
      TesterInput,
      ArticleInput,
      BatchInput,
      OrderInput,
      ArticleNumberInput,
      SaveButton,
      SetButton
    },

  };
  </script>
  


<style>

  /* CSS-Styling für die Komponente */
  .btn {
    /* Stile für die Buttons */
    border: 1px solid #4a5568; /* Ändere die Farbe nach Bedarf */
    background-color: #2d3748; /* Ändere die Farbe nach Bedarf */

    padding: 0.5rem 1rem; /* Ändere die Größe nach Bedarf */
    border-radius: 0.25rem; /* Ändere die Rundung nach Bedarf */
    cursor: pointer;
  }
  .btn:hover {
    background-color: #1a202c; /* Ändere die Farbe nach Bedarf */
  }

  input {
    text-align: center;
  }

  .sample-number-heading {
  /* Styles for the sample number heading */
  font-size: 24px; /* Adjust the size as needed */
  font-weight: bold; /* Adjust the weight as needed */
}

@media print {
  .hidden-print {
    display: none !important;
  }

  a[href]:after {
    content: none !important;
  }

  header, footer {
    display: none;
  }
}

</style>

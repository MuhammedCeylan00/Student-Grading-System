<template>
  <div class="hello">
    <h2>Kirklareli University</h2>
    <hr />
    <label id="file">Dosya Yükle
      <input type="file" name="Select File" @change="handleFileUpload($event)" />
    </label>
    <br>
    <button id="viewData" v-if="studentData.length > 0" @click="viewTable()">Sınıf Bilgileri (Göster/Kapat)</button>
    <button id="viewData" v-if="studentData.length > 0" @click="viewStudent()">Öğrenci Kart (Göster/Kapat)</button>
    <div class="container" v-if="viewTableState">
      <div class="table">
        <button class="saveStudent" @click="saveStudent">Öğrencileri Sisteme Kaydet</button>
        <h2>Toplam Öğrenci Sayısı : {{ studentData.length }}</h2>
        <table border="1" cellspacing="0">
          <tr>
            <th>Ders Adı</th>
            <th>Öğretim Elemanı Adı</th>
            <th>Sınav Tipi</th>
            <th>Sınav Tarihi</th>
            <th>Sınav Saati</th>
          </tr>
          <tr>
            <td>{{ lessonData[0]["Ders Adı"] }}</td>
            <td>{{ lessonData[0]["Öğretim Elemanı Adı"] }}</td>
            <td>{{ lessonData[0]["Sınav Tipi"] }}</td>
            <td>{{ lessonData[0]["Sınav Tarihi"] }}</td>
            <td>{{ lessonData[0]["Sınavın Saati"] }}</td>
          </tr>
        </table>
        <table border="1" cellspacing="0">
          <thead>
            <th>SN</th>
            <th>Öğrenci Numarası</th>
            <th>Adı Soyadı</th>
          </thead>
          <tbody>
            <tr v-for="(item, index) in studentData" :key="item">
              <td>{{ index+ 1 }}</td>
              <td>{{ item["Öğrenci No"] }}</td>
              <td>{{ item["Adı Soyadı"] }}</td>
            </tr>
          </tbody>
        </table>
      </div>
      <div class="clasess">
        <h3> Kontenjana Göre sınıf seçin</h3>
        <h4>{{ classselectMessage }}</h4>
        <table border="1" cellspacing="0">
          <thead>
            <tr>
              <th>Sınıf</th>
              <th>Kapasite</th>
              <th>Seç</th>
              <th> Exel Çıktısı</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in classes" :key="index">
              <td>{{ item.sinif }}</td>
              <td>{{ item.kapasite }}</td>
              <td>
                <input type="checkbox" v-model="selectedClasses" :value="item.sinif" :id="item.sinif"
                  :name="item.sinif">
                <label :for="item.sinif"></label>
              </td>
              <td><button v-if="selectedClassesResult.includes(item.sinif)" class="downloadsButton"
                  @click="exportExel(item.sinif)"> <i class="fas fa-download"></i></button></td>
            </tr>
          </tbody>
        </table>
        <button class="saveStudent" @click="processSelectedClasses">Öğrencileri Rastgele sınıflara kaydet</button>
      </div>
      <div v-if="exportExelDataState" class="classAndStudent">
        <button class="saveStudent" @click="download">excel çıktı al</button>
        <table id="exportExel" border="1" cellspacing="0">
          <thead>
            <tr>
              <th colspan="4">
                <p>Kırklareli Üniversitesi
                  {{ lessonData[0]["Ders Adı"] }} {{ lessonData[0]["Sınav Tipi"] }}<br>
                  {{ lessonData[0]["Sınav Tarihi"] }} {{ lessonData[0]["Sınavın Saati"] }} <br>
                  {{ lessonData[0]["Öğretim Elemanı Adı"] }}<br>
                  {{ exportClassName }} Numaralı Sınıf</p>
              </th>
            </tr>
            <tr>
              <th>Sıra No</th>
              <th>Öğrenci No</th>
              <th>Ad Soyad</th>
              <th>İmza</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in exportExelData" :key="index">
              <td>{{ index+ 1 }}</td>
              <td>{{ item.namesurname }}</td>
              <td>{{ item.ogrno }}</td>
              <td></td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <div v-if="viewStudentState">
      <div>
        <form @submit.prevent="submitForm">
          Okul Numaranızı Girip Sınava Giriş kartınıza ulaşın.<br>
          <label for="schoolNumber">Okul Numarası:</label>
          <input type="text" v-model="schoolNumber" id="schoolNumber" name="schoolNumber" required>
          <button @click="submitForm" type="submit">Gönder</button>
        </form>
      </div>
      <div  class="studentCard" v-if="studentCardData.length>0">
      <table id="studentCardTable"   border="1" cellspacing="0">
        <thead>
          <tr>
            <th><img src="../assets/uni_logo.gif"></th>
            <th>Kırklareli Üniversitesi Sınava giriş Kartı</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <th>öğrenci No: </th>
            <th>{{studentCardData[0].ogrno}}</th>
          </tr>
          <tr>
            <th>Adı Soyadı: </th>
            <th>{{ studentCardData[0].namesurname }}</th>
          </tr>
          <tr>
            <th>Ders: </th>
            <th>{{ lessonData[0]["Ders Adı"] }}</th>
          </tr>
          <tr>
            <th>Sınav Tipi</th>
            <th>{{ lessonData[0]["Sınav Tipi"] }}</th>
          </tr>
          <tr>
            <th>Tarih: </th>
            <th>{{ lessonData[0]["Sınav Tarihi"] }} {{ lessonData[0]["Sınavın Saati"] }}</th>
          </tr>
          <tr>
            <th>Sınava Girilecek Sınıf: </th>
            <th>{{ studentCardData[0].class}}</th>
          </tr>
        </tbody>
      </table>
      <button class="saveStudent" @click="downloadImage">Kartı indir.</button>
    </div>
    </div>
  </div>
</template>

<script>
import html2canvas from 'html2canvas';
import * as XLSX from "xlsx";
import axios from "axios"
export default {
  name: 'HelloWorld',
  data() {
    return {
      studentCardData: [],
      schoolNumber: '',
      viewStudentState: false,
      exportExelDataState: false,
      exportClassName: "",
      exportExelData: [],
      classButtonLoopControlArray: [],
      classesResult: {},
      classAndStudents: [],
      mixedSeries: [],
      selectedClasses: [],
      selectedClassesResult: [],
      students: [],
      viewTableState: false,
      file: File,
      arrayBuffer: null,
      dataList: [],
      studentData: [],
      lessonData: [{ "Ders Adı": "", "Öğretim Elemanı Adı": "", "Sınav Tipi": "", "Sınav Tarihi": "", "Sınavın Saati": "" }],
      classes: [
        {
          "sinif": 101,
          "kapasite": 38
        },
        {
          "sinif": 102,
          "kapasite": 40
        },
        {
          "sinif": 103,
          "kapasite": 34
        },
        {
          "sinif": 104,
          "kapasite": 30
        },
        {
          "sinif": 105,
          "kapasite": 40
        },
        {
          "sinif": 106,
          "kapasite": 24
        },
        {
          "sinif": 107,
          "kapasite": 24
        },
        {
          "sinif": 108,
          "kapasite": 30
        },
        {
          "sinif": 109,
          "kapasite": 34
        },
        {
          "sinif": 201,
          "kapasite": 38
        },
        {
          "sinif": 202,
          "kapasite": 40
        },
        {
          "sinif": 203,
          "kapasite": 34
        },
        {
          "sinif": 204,
          "kapasite": 20
        },
        {
          "sinif": 205,
          "kapasite": 20
        },
        {
          "sinif": 206,
          "kapasite": 50
        },
        {
          "sinif": 207,
          "kapasite": 44
        },
        {
          "sinif": 208,
          "kapasite": 22
        },
        {
          "sinif": 209,
          "kapasite": 20
        }
      ]
    }
  },
  methods: {
    handleFileUpload(event) {
      this.file = event.target.files[0];
      console.log("file: ", this.file.name.slice(-3))
      if (this.file.name.slice(-3) != "xls") {
        alert("HATA ! Yüklenen dosya formatı Exel olmalıdır.")
        location.reload();
      } else if (this.file.name.slice(-3) == "xls") {
        let fileReader = new FileReader();
        fileReader.readAsArrayBuffer(this.file);
        fileReader.onload = () => {
          this.arrayBuffer = fileReader.result;
          var data = new Uint8Array(this.arrayBuffer);
          var arr = new Array();
          for (var i = 0; i != data.length; ++i)
            arr[i] = String.fromCharCode(data[i]);
          var bstr = arr.join("");
          var workbook = XLSX.read(bstr, { type: "binary", cellDates: true, dateNF: "dd-mm-yyyy" });
          var first_sheet_name = workbook.SheetNames[0];
          var worksheet = workbook.Sheets[first_sheet_name];
          console.log(XLSX.utils.sheet_to_json(worksheet, { raw: true }));
          //this.dataList = XLSX.utils.sheet_to_json(worksheet, { raw: true, dateNF: "yyyy-mm-dd", cellDates: true });
          this.dataList = XLSX.utils.sheet_to_json(worksheet, { raw: true });
          console.log("dataList: ", this.dataList);

          //Set Lesson Data
          const lessonName = Object.keys(this.dataList[0])[4]
          const teacherName = this.dataList[0][lessonName]
          const examType = this.dataList[1][lessonName];
          const examDate = this.dataList[2][lessonName];
          const examTime = this.dataList[3][lessonName];

          this.lessonData[0]["Ders Adı"] = lessonName;
          this.lessonData[0]["Öğretim Elemanı Adı"] = teacherName;
          this.lessonData[0]["Sınav Tipi"] = examType;
          this.lessonData[0]["Sınav Tarihi"] = examDate.toLocaleDateString("tr-TR", {
            day: "2-digit",
            month: "2-digit",
            year: "numeric"
          });
          this.lessonData[0]["Sınavın Saati"] = examTime.toLocaleTimeString("tr-TR", {
            hour: "2-digit",
            minute: "2-digit"
          })

          var student = { "Adı Soyadı": "", "Öğrenci No": null }

          for (let i = 0; i < this.dataList.length; i++) {
            student["Adı Soyadı"] = this.dataList[i]["Adı Soyadı"];
            student["Öğrenci No"] = this.dataList[i]["Öğrenci No"];
            console.log("***********");
            this.studentData.push(student);
            this.mixedSeries.push(student);
            student = { "Adı Soyadı": "", "Öğrenci No": null }
          }
          //lessonData
          console.log("lesson data: ", this.lessonData);
        };
      }
    },
    viewTable() {
      if (this.viewTableState == false) {
        this.viewTableState = true;
      } else {
        this.viewTableState = false
      }
    },
    saveStudent() {
      axios.post("http://localhost:3200/student", {
        title: this.studentData,
      })
        .then(response => {

          console.log(response.data);
        })
        .catch(error => {
          console.log(error);
        });
    },
    processSelectedClasses() {

      //Sınıf ve kontenjan key value olarak ayırtıldı.

      for (var i = 0; i < this.classes.length; i++) {
        var sinif = this.classes[i].sinif;
        var kapasite = this.classes[i].kapasite;
        this.classesResult[sinif] = kapasite;
      }
      this.mixedSeries.sort(() => Math.random() - 0.5);

      this.classButtonLoopControlArray = Object.values(this.selectedClasses);

      this.selectedClassesResult = [];
      var toplam = 0
      for (let i = 0; i < this.selectedClasses.length; i++) {
        console.log(this.classesResult[this.selectedClasses[i]]);
        toplam += this.classesResult[this.selectedClasses[i]]
        this.selectedClassesResult.push(this.selectedClasses[i]);
        console.log("samet:");
        if (toplam == this.studentData.length) {
          break;
        } else if (toplam > this.studentData.length) {
          break;
        }
      }

      if (toplam < this.studentData.length) {
        this.selectedClassesResult = [];
        alert("Kapasite öğrenci Sayısından az olmamalıdır.")
      } else {
        console.log("result:    ", this.classesResult)
        console.log("seçilen sınıflar: ", this.selectedClassesResult);



        for (let i = 0; i < this.selectedClassesResult.length; i++) {
          console.log("kapasite: ", this.classesResult[this.selectedClassesResult[i]]);
          for (let j = 0; j < this.classesResult[this.selectedClassesResult[i]]; j++) {
            toplam++;
            console.log("merhaba: ", j);
            if (this.mixedSeries.length != 0) {
              var data = { "namesurname": this.mixedSeries[0]["Adı Soyadı"], "ogrno": this.mixedSeries[0]["Öğrenci No"], "class": this.selectedClassesResult[i] };
              this.classAndStudents.push(data);
              this.mixedSeries.shift();
            }
          }

        }
      }

      console.log("sınıf öğrenci: ", this.classAndStudents);
      axios.post("http://localhost:3200/studentclass", {
        title: this.classAndStudents,
      })
        .then(response => {

          console.log(response.data);
        })
        .catch(error => {
          console.log(error);
        });
    },
    exportExel(item) {
      this.exportExelDataState = true;
      this.exportClassName = item;
      this.exportExelData = [];
      for (let a = 0; a < this.classAndStudents.length; a++) {
        if (this.classAndStudents[a]["class"] == item) {
          this.exportExelData.push(this.classAndStudents[a]);
        }
      }
      console.log("item: ", item);
      console.log(this.exportExelData);
      this.download;

    },

    download() {
      const table = document.getElementById("exportExel")
      const ws = XLSX.utils.table_to_sheet(table)
      ws['!rows'] = [{ hpx: 100 }, { hpx: 'auto' }, { hpx: 'auto' }]
      ws['!cols'] = [{ wch: 8 }, { wch: 50 }, { wch: 'auto' }]
      const wb = XLSX.utils.book_new()
      XLSX.utils.book_append_sheet(wb, ws, 'Sheet1')
      XLSX.writeFile(wb, 'data.xlsx')


    },
    viewStudent() {
      if (this.viewStudentState == false) {
        this.viewStudentState = true;
      } else {
        this.viewStudentState = false;
      }

    },
    async submitForm() {
      var url = "http://localhost:3200/student/" + this.schoolNumber;
      let self = this;
      axios
        .get(url)
        .then(function (response) {
          self.studentCardData = response.data;
          console.log("dsdsdsd", self.studentCardData);
          
        });
    },
    downloadImage() {
      const studentCardTable = document.getElementById("studentCardTable");
      html2canvas(studentCardTable).then(canvas => {
        const link = document.createElement('a');
        link.download = 'studentCard.png';
        link.href = canvas.toDataURL();
        link.click();
      });
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#file label {
  size: 100px;
}

#file input {
  margin-top: 35px;
  margin-left: 10px;
  background-color: rgb(255, 255, 255);
  color: rgb(0, 0, 0);
  align-items: center;
  text-align: center;
  padding: 10px;
  font-size: 13px;
  border-radius: 20px;
  box-shadow: 5px 5px 5px 5px rgb(83, 79, 79)
}

#file input:hover {
  background-color: rgb(77, 192, 77);
  color: white;
}

#viewData {
  margin-top: 50px;
  background-color: rgb(77, 192, 77);
  color: white;
  font-size: 15px;
  margin-left: 80px;
  border: 1px solid white;
  ;
  border-radius: 20px;
  padding: 10px;
}

#viewData:hover {
  background-color: white;
  color: rgb(77, 192, 77);
  border: 1px solid rgb(77, 192, 77);
}

table {
  margin: 20px;
}

table tbody td {
  text-align: left;
  padding-left: 10px;
}

.saveStudent {
  float: left;
  background-color: rgb(77, 192, 77);
  height: 3rem;
  border: 1px solid rgb(255, 255, 255);
  color: white;
  border-radius: 20px;
  padding: 10px;
  cursor: pointer;
}

.saveStudent:hover {
  background-color: white;
  color: rgb(77, 192, 77);
  border: 1px solid rgb(77, 192, 77);
}

.table {
  margin-top: 40px;
  display: flex;
  flex-direction: column;
  width: 30%;
}

.container {
  display: flex;
  flex-direction: row;
  padding: 20px;
}

.clasess {
  margin-top: 120px;
  margin-left: 100px;
}

.clasess table {
  width: 30%;
}

.downloadsButton {
  cursor: pointer;
}

.saveStudent {
  margin-left: 20px;
}

.classAndStudent {
  margin-top: 50px;
  height: 50%;
  display: flex;
  flex-direction: column;
  margin-left: 40px;
}

.studentCard {
  padding: 10px;
  margin-left: 37%;
  padding-top: 40px;
}

form {
  margin-top: 50px;
  border: 1px solid black;
  width: 32%;
  margin-left: 39%;
  background-color: beige;
  padding: 10px;
}

form button {
  height: 30px;
  background-color: green;
  color: white;
  border: 1px solid white;
}
</style>

# Prediksi Mahasiswa Baru - Decision Tree & Random Forest
## Panduan Penggunaan Model Prediksi Mahasiswa

1. **Unduh Model**:
   - Klik pada tautan berikut untuk mengunduh model: [best_random_forest_model.pkl].

2. **Penggunaan di Python**:
   - Langkah-langkah untuk menggunakan model dalam Python:
   
     ```python
     import joblib

     # Mengimpor model
     model = joblib.load('best_random_forest_model.pkl')

     # Lakukan prediksi dengan data baru
     hasil_prediksi = model.predict(data_baru)
     print(hasil_prediksi)
     ```
     
     Gantilah `data_baru` dengan data yang ingin Anda gunakan untuk prediksi.

3. **Penggunaan di Laravel 8**:
   - Buat Model Reader `model_reader.py`:
   
     ```python
     import joblib
     import json
     import sys
     import os

     def convert_dict_to_float(input_dict):
         for key, value in input_dict.items():
             try:
                 input_dict[key] = float(value)
             except (ValueError, TypeError):
                 pass
         return input_dict

     def main(input_data):
         try:
             input_dict = json.loads(input_data)
            
             # Convert dictionary values to float
             input_dict = convert_dict_to_float(input_dict)

             # Load the trained model
             model_path = 'path_to/best_random_forest_model.pkl'
             model = joblib.load(model_path)

             # Predict using the model
             predictions = model.predict([list(input_dict.values())])

             # Convert predictions to a list
             predictions_list = predictions.tolist()

             # Return predictions as JSON
             output = json.dumps({'predictions': predictions_list})
             print(output)

         except Exception as e:
             error_message = str(e)
             print(json.dumps({'error': error_message}))

     if __name__ == "__main__":
         input_data = sys.argv[1]
         main(input_data)
     ```
   
   - Buat Controller:
   
     ```php
     // Isi dari fungsi predict() di controller Anda
     public function predict(Request $request)
     {
         $input_data = $request->all();

         $input_data_json = json_encode($input_data);

         $command = [
             'python3',             // Command to run Python 3
             public_path('models/model_reader.py'),  // Path to your Python script
             $input_data_json  // JSON-encoded input data
         ];
         // Create a new Process instance
         $process = new Process($command);

         // Run the process
         $process->run();
         // Check if the process ran successfully
         if (!$process->isSuccessful()) {
             throw new ProcessFailedException($process);
         }

         $output = $process->getOutput();
         $predictions = json_decode($output);

         return response()->json(['predictions' => $predictions]);
     }
     ```

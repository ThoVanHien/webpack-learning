- npm init --y
- npm i webpack webpack-cli --save-dev 
- Trong file package.json: thiết lập private: true để không up lên thành thư viện npm registry và xoá file main

- Gây ra 1 số vấn đề: 
   - Forced to download code không cần thiết
   - Sai thứ tự hoặc thiếu sẽ làm app bạn không hoạt động đúng 
   - Khó hiểu và không rõ ràng dùng script

- Di chuyển index.html vào thư mục dist và cài loadash vào devDependencies
- Chỉnh lại script trong file index.html và dùng import trong file index.js:
   - Import và export trong es6 và trình duyệt hiện đại có thể hiểu được. Tuy nhiên chỉ hỗ trợ với module js còn các file khác như hình ảnh thì không được
- Chạy lệnh 'npx webpack', default(4x trở lên không yêu cầu file config) như sau:
   - src/index.js build ra thành dist/main.js
   - Tới đây sẽ có thắc mắc về npx: nếu không có sẽ cài tạm vô node_modules của dự án, webpack-cli thì cài vào devDependencies. Nếu đứng ngoài sẽ được tải và lưu trong cache của npx (~/.npm/_npx/<hash>/node_modules). Để xóa cache npx: npx clear-npx-cache --all
   - Webpack chỉ thay đổi import và export thôi, còn phần khác phải dùng babel thông qua webpack loaders 

- Tạo file webpack.config.js - file này được đọc ở node_modules/webpack-cli/lib/webpack-cli.js hàm loadConfig :
   - Có thể dùng lệnh npx webpack --config webpack.config.js
   - Shortcut npm run build : webpack


- Quản lý asset: 
   - Sửa lại script: `<script src="bundle.js"></script>`
   - Cài đặt: `npm install --save-dev style-loader css-loader`
   
   - XỬ LÝ CSS
   - Thêm module.rules(test: dùng regex để tìm file, use: dùng loader nào để xử lý file đó) trong webpack:
      - test: /\.css$/i
      - style-loader: xử lý import vào js
      - css-loader: thêm css vào DOM  
      - import vào file js, nếu không import là không được build. => import vào thẻ style file html

   -  XỬ LÝ IAMGES:
   - Tương tự như css thêm test và type:
      - test: /\.(png|svg|jpg|jpeg|gif)$/i
      - type: 'asset/resource'
      - import vào css và file js đều được, nó sẽ build ra hình trong thư mục dist

   - XỬ LÝ FONTS:
      - test: /\.(woff|woff2|eot|ttf|otf)$/i,
      - type: 'asset/resource',
      - Có thể kết hợp với @font-face

   - XỬ LÝ FILE JSON(default), CSVS, TSV XML. 3 cái này dùng như sau:
      - Nhớ cài loader: npm install --save-dev csv-loader xml-loader
      - File json thì mặc định cho rồi nhưng dùng import default thôi. Dùng {} sẽ warning
      - test: /\.(csv|tsv)$/i,
      - use: ['csv-loader'],

      - test: /\.xml$/i,
      - use: ['xml-loader'],
   - MUỐN LOAD FILE NÀO THÌ TẢI LOADER FILE ĐỊNH DẠNG ĐÓ
   - LƯU Ý: CÓ THỂ NHÓM ASSETS LẠI CHO GỌN
   - PLUGINS:
      - html-webpack-plugin: build html luôn và tự thay đổi script link tới file entry hữu dụng với output dùng name và hash
   
   - CÁC KỸ THUẬT:
      - clean: true trong output 
      - devtool: inline-source-map // dùng để debug dễ hơn
      - optimization.splitChunks: 'all', // dùng để đặt nhân tử chung với 2 file dùng 1 package. 
      - devServer.static: './dist', // thêm optimization.runtimeChunk: 'single' nếu nhiều hơn 1 entrypoint. Thêm shortcut này vào package.json : "start": "webpack serve --open",

   - TYPESCRIPT: https://webpack.js.org/guides/typescript/
   - VẤN ĐỀ VỀ CATCHING: 
      - Khi ở phía server không có gì thay đổi về CSS và JS thì phía client sẽ lưu lại trên cache trình duyệt (cache tĩnh)
      - Cache động thì cần phải cập nhật trước khi truy xuất => lâu hơn cache tĩnh. Nên dùng cho lưu kết quả tìm kiếm google, dữ liệu ứng dụng
      - Khi dùng output với hash thì mỗi lần build(dù không thay đổi) sẽ tạo ra một file mới. Dùng optim
   
   - TRUYỀN BIẾN QUA NPX VÀ NPM: tham khảo https://webpack.js.org/api/cli/#commands
      - Khi webpack config sử dụng exports bằng function thì "environment" sẽ được truyền như sau:
         - npx webpack --env prod (args sẽ nhận được {prod: true})
         - npx webpack --node-env production   # process.env.NODE_ENV = 'production'
         - module.exports(env, args) #log ra để xem thử



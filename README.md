# express.js
var MovieSchema = new Schema({
    title: {
        type: String,
        required: true
    },
    description: {
        type: String
    },
    genre: {
        type: String
    },
    release_date: {
        type: Date,
        required: true
    }
});
const userSchema = new Schema({
    name: { type: String, required: true },
    email: { type: String, required: true, unique: true },
    password: { type: String, required: true, minlength: 6 },
    favourite_movies: [{
        type: Schema.Types.ObjectId,
        ref: "Movie"
    }]
});
 MovieModel.aggregate(
  [
    {
      $lookup: {
        from: "users", // user collection name
        localField: "_id",
        foreignField: "favourite_movies",
        as: "user",
      },
    },
    {
      $addFields: {
        count: { $size: "$user" },
      },
    },
    {
      $sort: { count: -1 },
    },
    {
      $unset: ["user", "count"],
    },
  ],
  (err, data) => {
    if (err) console.log(err);
    else console.log(data);
  }
);

movieRouter
    .route("/")
    .get((req, res, next) => {
        Movie.find()
            .then((movies) => {
                res.statusCode = 200;
                res.setHeader("Content-Type", "application/json");
                res.json(movies);
            })
            .catch((err) => next(err));
    })

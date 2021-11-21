<script>
import { h, toRefs } from "vue";
import { refreshDataEvent, storedMoviesLsKey } from "../constants";

export default {
  props: {
    movie: String,
    movieIndex: Number,
  },
  setup(props) {
    const { movie, movieIndex } = toRefs(props);

    // Editation methods
    const editMovie = (key, value) => {
      const moviesData = JSON.parse(
        window.localStorage.getItem(storedMoviesLsKey)
      );

      moviesData[movieIndex.value][key] = value;

      window.localStorage.setItem(
        storedMoviesLsKey,
        JSON.stringify(moviesData)
      );
    };
    const addListItem = (key, value) => {
      let moviesData = JSON.parse(
        window.localStorage.getItem(storedMoviesLsKey)
      );

      moviesData[movieIndex.value][key] = [value, ...moviesData[movieIndex.value][key]]

      window.localStorage.setItem(
        storedMoviesLsKey,
        JSON.stringify(moviesData)
      );
      window.dispatchEvent(refreshDataEvent);
    };
    const removeListItem = (key, value) => {
      let moviesData = JSON.parse(
        window.localStorage.getItem(storedMoviesLsKey)
      );

      moviesData[movieIndex.value][key] = moviesData[movieIndex.value][
        key
      ].filter((currentValue) => value !== currentValue);

      window.localStorage.setItem(
        storedMoviesLsKey,
        JSON.stringify(moviesData)
      );
      window.dispatchEvent(refreshDataEvent);
    };

    // Element render methods
    const buildElement = (data, attributes) => {
      let value, key;
      if (typeof data === "object") {
        key = data[0];
        value = data[1];
      } else value = data;

      const onChange = (e) => editMovie(key, e.target.value);
      const onClick = (inputId, key) => {
        let input = document.getElementById(inputId);

        if (input && input.value) {
          addListItem(key, input.value);
          input.value = "";
        }
      };
      const selectInputElement = (key) => {
        const id = `${key}-${movieIndex.value}`;
        return h("div", [
          h("input", { type: "text", id }),
          h("button", { type: "text", onClick: () => onClick(id, key) }, "Add"),
        ]);
      };
      const valueType = typeof value;
      const inputTypes = {
        string: (val) => ({
          element: "input",
          type: "text",
          value: val,
          onChange,
        }),
        number: (val) => ({
          element: "input",
          type: "number",
          value: val,
          onChange,
        }),
        object: (val) => ({
          element: "select",
          name: key,
          onChange: (e) => removeListItem(key, e.target.value),
          children: val.map((text) =>
            buildElement(text, {
              value: text,
              innerHtml: `${text} -- Delete ❌`,
            })
          ),
        }),
      };
      const isSelectElement = inputTypes[valueType](value).element === "select";
      const inputElement = h(
        attributes ? "option" : inputTypes[valueType](value).element,
        attributes || inputTypes[valueType](value),
        attributes?.innerHtml ||
          inputTypes[valueType](value).children ||
          undefined
      );
      return attributes
        ? inputElement
        : h("div", [
            h("label", { for: key }, `${key.toLocaleUpperCase()}: `),
            inputElement,
            ...(isSelectElement ? [selectInputElement(key)] : []),
          ]);
    };
    const movieInputElements = Object.entries(movie.value).map((movie) =>
      h("div", { class: "movie__input" }, buildElement(movie, null))
    );

    return () =>
      h("div", { class: "movie" }, [
        h("h2", `${movie.value.name}:`),
        h("div", movieInputElements),
      ]);
  },
};
</script>

<style>
.movie {
  display: flex;
  margin-bottom: 30px;
  border: 1px solid black;
  border-radius: 3px;
  padding: 20px;
  text-align: left;
}
.movie__input {
  margin-bottom: 20px;
  text-align: left;
}
h2 {
  width: 300px;
  margin: 0;
  padding-right: 20px;
}
input {
  line-height: 25px;
  border: 0;
  border-bottom: 1px solid;
  padding: 0;
}
button {
  height: 25px;
  margin: 5px 0 0 10px;
}
select {
  height: 25px;
}
</style>

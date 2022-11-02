## React hook form clear error
```tsx
// destructure reset from useForm as like below
const { register, handleSubmit, errors, reset } = useForm();
// then call the reset function when all of the data has been sent to the server
reset();
//example:
  const { register, handleSubmit, errors, reset } = useForm();
  const onSubmit = (data) => {
    const url = 'http://localhost:5000/item';
    axios
      .post(url, {
        ...data,
      })
      .then((res) => {
        if (res.data.acknowledged) {
          toast('Item Added Successfully...');
          reset(); // here I am reseting the form
        }
      });
  };
// react hook form reset only one field
resetField('input-name'); // register input and resetField works
```


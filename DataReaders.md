# Data Reader #

The data reader takes data from the subscriber, demarshals it into the appropriate type for that topic, and delivers the sample to the application. Each data reader is bound to a particular topic. The application uses the data reader’s type-specific interfaces to receive the samples. [(see DataReader in OpenDDS Developer Guide).](http://download.ociweb.com/OpenDDS/OpenDDS-latest.pdf)

# Default Data Readers #

Default Data Readers are included in the package and are located at [$QLDDS\_ROOT/Addins/OpenDDS](https://code.google.com/p/qldds/source/browse/Addins/OpenDDS).

Default Data Readers are autogenerated from QuantLibAddin/C++ XML function definitions using qldds\_gensrc.py.

Default Data Readers are derived from **[qldds::DataReaderListener](https://code.google.com/p/qldds/source/browse/qldds_utils/DataReaderListener.hpp)**

**[qldds::DataReaderListener](https://code.google.com/p/qldds/source/browse/qldds_utils/DataReaderListener.hpp)** is derived from **_DDS_::DataReaderListener** and it has a number of additional pure virtual functions that will be invoked in _DDS::DataReaderListener::on\_data\_available_.

Invoked calls

| **Call** | **Description** |
|:---------|:----------------|
| on\_reading\_start | Reading Start |
|  on\_reading\_end | Reading End|
| pre\_quantlib\_addin\_call | Invoked before QuantLibAddin call |
| post\_quantlib\_addin\_call | Invoked after QuantLibAddin call |
| on\_std\_exception | Invoked on QuantLib Exception |
| on\_dds\_reading\_error | Invoked on DDS reading error |
| on\_dds\_exception | Invoked on DDS Exception |

Below is an incomplete class declaration of a default data reader for qlSwapRateHelper2 data type.

The complete class declaration can be found in [$QLDDS\_ROOT/Addins/OpenDDS/ratehelpersDataReaderListenerImpl.hpp](https://code.google.com/p/qldds/source/browse/Addins/OpenDDS/ratehelpersDataReaderListenerImpl.hpp)

```
  class qlSwapRateHelper2DataReaderListenerImpl :  public virtual OpenDDS::DCPS::LocalObject
           <qldds::DataReaderListener< ratehelpers::qlSwapRateHelper2, std::string > >;
```

Note that qldds::DataReaderListener takes two template parameters.

  * First parameter indicates QLDDS Data Type which is _ratehelpers::qlSwapRateHelper2_ in case of _qlSwapRateHelper2DataReaderListenerImpl_.

  * Second parameter indicates the type returned by a particular QuantLibAddin call which is a _std::string_ returned by _QuantLibAddinCpp::qlSwapRateHelper2_ in this case.

> A reference to an instance of an object of the first template parameter is being passed to _pre\_quantlib\_addin\_call_ and _post\_quantlib\_addin\_call_.   A reference to an instance of an object of the second template parameter is being passed to _post\_quantlib\_addin\_call._

Below is a snapshot of an autogenerated code of _on\_data\_available_ for _qlSwapRateHelper2DataReaderListenerImpl_. Complete declaration can be found in $QLDDS\_ROOT/Addins/OpenDDS/ratehelpersDataReaderListenerImpl.cpp

```

ratehelpers::qlSwapRateHelper2 obj; 
DDS::SampleInfo si; 
DDS::ReturnCode_t status = obj_dr->take_next_sample(obj, si);

if ( status == DDS::RETCODE_OK )
{
  if ( si.valid_data == true )
  {
    ++count;
    if ( pre_quantlib_addin_call( reader, si, obj ) )
    {
      std::string returnObject; 
      
      try 
      {
        ACE_Guard<ACE_Mutex> guard( get_ACE_Mutex() );

        returnObject = QuantLibAddinCpp::qlSwapRateHelper2 ( 
                                        obj.ObjectId.in(), 
                                        obj.Rate, 
                                        obj.Tenor.in(),
                                        obj.Calendar .in(), 
                                        obj.FixedLegFrequency.in(), 
                                        obj.FixedLegConvention.in(), 
                                        obj.FixedLegDayCounter.in(), 
                                        obj.IborIndex.in(), 
                                        obj.Spread, 
                                        obj.ForwardStart.in(), 
                                        obj.DiscountingCurve.in(), 
                                        static_cast<bool>(obj.Permanent), 
                                        obj.Trigger, 
                                        static_cast<bool>(obj.Overwrite) 
                                 );

       } catch ( std::exception& e )
       { 
         on_std_exception( reader, obj, e ); 
         continue;
       }

       if ( !post_quantlib_addin_call( reader, obj, returnObject ) ) 
         break;
}

```